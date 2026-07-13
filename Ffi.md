The option you didn't consider — and it's the right one
Flutter UI + F# core as a native library via dart:ffi. Compile the F# engine with NativeAOT to a shared library (.so/.dylib), and have Flutter call it through FFI. You get Flutter's native UI and keep exactly ONE verdict engine — the same AOT binary the CLI uses, the same one that's already agreement-proven. No Fable→Dart. No third runtime. No decimal question (real .NET decimal, real System.Decimal arithmetic). This is strictly better than transpiling, and it's how serious cross-platform apps embed a trusted core. How this idea works from claude

Gpt
Claude’s proposal is architecturally better than Fable-to-Dart for GSTFlow’s trusted verdict path.

I should have considered it as the primary option.

Corrected verdict

\boxed{
\text{Flutter UI}
+
\text{NativeAOT F\# engine through C ABI}
}

gives you:

Flutter’s strong mobile and desktop UI ecosystem;

the real .NET/F# implementation;

System.Decimal;

the same canonical serializer and SHA-256 implementation;

no generated Dart domain model;

no Fable-Dart runtime dependency;

a very small trust boundary.


Flutter officially supports calling C-compatible native libraries through dart:ffi, and .NET NativeAOT can publish functions as native C entry points using UnmanagedCallersOnly. 

One correction to Claude’s wording

It will not literally be the same binary on every platform.

You need platform- and architecture-specific artifacts:

Windows x64     gstflow.dll
Windows ARM64   gstflow.dll
Linux x64       libgstflow.so
Linux ARM64     libgstflow.so
Android arm64   libgstflow.so
Android x64     libgstflow.so
macOS ARM64     libgstflow.dylib / static library
iOS ARM64       statically linked native library

The accurate claim is:

> The CLI and Flutter native libraries use the same F# engine source, rules, serializer, tests and versioned build inputs.



The CLI executable and shared library are different publish artifacts, but they can be generated from the same pinned core commit.

Why it is superior to Fable-Dart

Concern	Fable → Dart	NativeAOT + FFI

F# domain semantics	Translated	Native
decimal behavior	Must prove translation	Real System.Decimal
SHA-256 implementation	Separate target implementation	Same .NET implementation
Canonical JSON	Potential runtime differences	Same implementation
Generated runtime types	Yes	No
Fable compiler dependency	Yes	No
Flutter ecosystem	Yes	Yes
Native packaging complexity	Moderate	Higher
Web support	Yes through Fable JS	No through dart:ffi
Trust surface	Three runtimes	Two runtimes with narrow C ABI


For GSTFlow, the native packaging cost is worth paying because correctness matters more than avoiding build matrices.

Recommended architecture

Flutter UI
    │
    │ Dart FFI — narrow C ABI
    ▼
GSTFlow.NativeExports
    │
    │ calls ordinary managed APIs
    ▼
GSTFlow.Core / CanonFlow.Core
    │
    ├── parsing
    ├── GST laws
    ├── decimal arithmetic
    ├── canonical JSON
    ├── SHA-256
    └── VerdictEnvelope

Important implementation detail

Do not export the F# functions directly unless the NativeAOT export requirements are proven to work cleanly with the generated F# methods.

Create a tiny C# NativeAOT export host:

GSTFlow.Core          F# authoritative engine
CanonFlow.Core        F# verification kernel
GSTFlow.NativeExports C# C-ABI façade only

Why C# for the façade?

NativeAOT exports only methods in the published assembly that are explicitly marked with UnmanagedCallersOnly and a non-empty EntryPoint. Methods residing only in referenced projects or packages are not automatically exported. 

The C# layer contains no laws. It only converts pointers into bytes, calls F#, and returns bytes.

Keep the ABI extremely small

Do not expose every F# record, union and decimal as C structs.

Expose UTF-8 JSON in and UTF-8 JSON out:

int32_t gstflow_validate(
    const uint8_t* request,
    intptr_t request_length,
    uint8_t** response,
    intptr_t* response_length);

void gstflow_free(
    uint8_t* response);

const char* gstflow_engine_info(void);

Even better, use explicit owned buffers:

typedef struct GstFlowBuffer {
    uint8_t* data;
    intptr_t length;
} GstFlowBuffer;

int32_t gstflow_validate_json(
    const uint8_t* input,
    intptr_t input_length,
    GstFlowBuffer* output);

void gstflow_buffer_free(GstFlowBuffer buffer);

The input request might be:

{
  "protocolVersion": "gstflow-native/1",
  "operation": "validateInvoice",
  "payload": {}
}

The output is the canonical verdict envelope.

Why JSON is still right

JSON across FFI does not create a second verdict engine.

It provides:

ABI stability;

no complex struct marshalling;

no F# union representation leakage;

easy fuzzing;

easy protocol versioning;

direct reuse of agreement fixtures;

freedom to change internal F# types;

simpler Dart code generation.


The F# side must own both parsing and canonical output.

Rules for the native export layer

1. Never let exceptions cross FFI

Catch every exception inside the NativeAOT host:

[UnmanagedCallersOnly(EntryPoint = "gstflow_validate_json")]
public static unsafe int Validate(
    byte* input,
    nint inputLength,
    GstFlowBuffer* output)
{
    try
    {
        // Copy request, call F# engine, allocate response.
        return 0;
    }
    catch (Exception ex)
    {
        // Return a stable internal-error envelope.
        return 1;
    }
}

Crossing an unmanaged boundary with an uncaught managed exception can terminate or corrupt the process contract.

2. Allocate and free on the same side

If NativeAOT allocates response memory, NativeAOT must provide the corresponding free function.

Do not allocate in .NET and free using Dart’s allocator—or vice versa.

3. Use byte lengths, not null-terminated strings

Invoice JSON may contain Unicode. Use UTF-8 bytes plus explicit lengths.

Avoid relying on:

char*

with implicit termination for main request/response payloads.

4. Never expose .NET object pointers

Do not return:

F# records;

managed strings;

GC handles;

decimal structs;

union objects.


All such values remain inside the engine.

5. Version the native protocol

Provide:

gstflow_abi_version
gstflow_engine_version
gstflow_rule_pack_version
gstflow_build_commit

The Flutter shell should refuse an unsupported ABI version.

What still needs proof

Using the real F# engine removes the largest semantic risks, but it does not eliminate all platform concerns.

NativeAOT compatibility

NativeAOT performs trimming and restricts unconstrained reflection. Libraries that depend on runtime code generation or broad reflection can require annotations, source generation, or replacement. Microsoft recommends minimizing reflection and resolving all AOT/trimming warnings. 

Your verdict path should already be mostly pure and reflection-free, which makes it a good NativeAOT candidate.

Make this law:

NativeAOT warnings = errors
Trimming warnings = errors

Platform build matrix

Every target requires a publish RID and native packaging path.

For example:

dotnet publish -c Release -r win-x64
dotnet publish -c Release -r linux-x64
dotnet publish -c Release -r android-arm64
dotnet publish -c Release -r osx-arm64

The exact supported NativeAOT RID/build-host combinations must be validated in CI rather than assumed.

Android

Package one .so per ABI:

android/src/main/jniLibs/arm64-v8a/libgstflow.so
android/src/main/jniLibs/x86_64/libgstflow.so

Test at least:

arm64 physical device;

x64 emulator;

release build;

background isolate calls;

repeated calls and memory use.


Apple platforms

Apple platforms deserve a separate spike.

iOS generally expects code to be compiled ahead of time and packaged according to its static/dynamic linking and signing rules. Do not assume that the exact Android shared-library layout transfers unchanged to iOS. Produce an iOS static/native artifact and integrate it through the application build.

This is feasible in principle, but must be proven before declaring six-platform support.

Web

dart:ffi works on Dart Native targets, not normal Flutter Web execution. Dart’s official documentation describes FFI for Dart Native mobile, command-line and server applications. 

Therefore:

Flutter Android/Desktop/iOS
    → NativeAOT FFI

Hosted GSTFlow web playground
    → Fable JavaScript or WASM

You still need a separate browser target.

That does not violate One Engine if browser agreement is enforced against the NativeAOT oracle.

Revised One-Engine model

The clean model is:

F# source
                       │
            ┌──────────┴──────────┐
            │                     │
      NativeAOT engine       Browser engine
            │                  Fable JS/WASM
      CLI + Flutter                │
            └──────── agreement ───┘

For native surfaces, there is genuinely one implementation artifact family.

For the browser, translation remains necessary unless you move to another browser-compatible .NET/WASM strategy.

Performance model

FFI overhead should be negligible if each call validates a complete document:

one JSON request
→ one FFI call
→ complete validation
→ one JSON response

Do not call FFI once per:

field;

line item;

rule;

UI keystroke.


Use coarse-grained operations:

validateInvoice
validateBatch
getEngineInfo
verifyPack

A single document validation is large enough that boundary overhead will be insignificant compared with parsing and rule execution.

Security and reliability implications

FFI introduces native-memory risks that pure Dart avoids. Flutter notes that native interop can introduce vulnerabilities if native code mishandles memory. 

Mitigate with:

bounded input size;

checked length conversions;

no arbitrary pointers;

no callbacks initially;

fuzzing malformed UTF-8 and JSON;

deterministic allocation ownership;

soak tests for leaks;

sanitizer testing where available;

no exported functions beyond the minimal ABI.


Better than Avalonia.FuncUI?

For your stated cross-platform ambition:

Flutter + NativeAOT FFI wins when:

Android and iOS matter;

polished mobile UI matters;

camera/OCR ecosystem matters;

Flutter is already chosen;

you want the true F# core.


Avalonia.FuncUI wins when:

Windows/Linux/macOS desktop is overwhelmingly primary;

one language and one build ecosystem matter most;

mobile quality is secondary;

the team strongly prefers F# UI.


With the FFI design, Flutter no longer sacrifices the trusted F# core. That removes my main reason for preferring Avalonia for GSTFlow.

Revised rating

Architecture	Rating

Flutter + Fable Dart	6.5/10
Avalonia.FuncUI	8.5/10 desktop, 6.5/10 mobile
Flutter + NativeAOT FFI	9/10 native cross-platform
Flutter + NativeAOT FFI + browser agreement target	9.5/10 overall


Recommended spike

Implement only these exports:

gstflow_abi_version
gstflow_engine_info
gstflow_validate_json
gstflow_free

Then prove:

1. Windows Flutter calls the DLL.


2. Android Flutter calls the ARM64 .so.


3. Same fixture passes through CLI and Flutter.


4. Output bytes are identical.


5. SHA-256 is identical.


6. 10,000 calls produce no memory growth.


7. Malformed input never crashes.


8. NativeAOT/trimming build has zero warnings.


9. Dart code imports no generated F# domain types.


10. Flutter contains no GST law implementation.



Final judgement

Claude’s central argument is right:

> Flutter + NativeAOT FFI is the best native-client architecture identified so far for GSTFlow.



I would only revise two phrases:

not “the same binary,” but the same authoritative F# engine compiled per platform;

not “strictly better” in every context, because it does not solve browser execution and creates native packaging complexity.


For Android, iOS and desktop, however, it is decisively better than transpiling the verdict engine to Dart.

Here is the corrected architecture I would adopt for GSTFlow.

GSTFlow Native Architecture

Flutter UI + NativeAOT F# Engine through "dart:ffi"

Executive decision

GSTFlow should use:

Flutter
    ↓
Small versioned C ABI
    ↓
NativeAOT export host
    ↓
GSTFlow.Core + CanonFlow.Core

This is the preferred architecture for Android, iOS, Windows, macOS and Linux.

The authoritative GST rules, decimal arithmetic, canonical serialization, hashing and verdict construction remain in F#/.NET. Flutter owns only presentation, document acquisition and user interaction.

The browser remains a separate target:

Hosted browser
    ↓
Fable JavaScript or .NET WebAssembly
    ↓
Agreement with NativeAOT oracle

This architecture removes Fable-Dart from the trusted verdict path without abandoning Flutter.

---

1. Precise architectural claim

Do not describe this as:

«One identical binary used by every platform.»

Describe it as:

«One authoritative F# engine source, compiled into platform-specific native artifacts from the same pinned release inputs.»

Typical outputs:

Windows x64       gstflow_native.dll
Windows ARM64     gstflow_native.dll
Linux x64         libgstflow_native.so
Linux ARM64       libgstflow_native.so
Android ARM64     libgstflow_native.so
Android x64       libgstflow_native.so
macOS ARM64       libgstflow_native.dylib
iOS ARM64         GSTFlowNative.framework

NativeAOT produces self-contained native artifacts, but publication remains specific to the target operating system and architecture.

Therefore the law is:

[
SameSource
\land
SameRules
\land
SameSerializer
\land
SameReleaseIdentity
]

not:

[
SamePhysicalFile
]

---

2. Repository structure

Use the following boundaries:

CanonFlow.Core/
    Generic verdict, evidence, canonical JSON and hashing

GSTFlow.Core/
    GST domain types, parsing, rules and reconciliation

GSTFlow.NativeExports/
    Small C# NativeAOT C-ABI façade

GSTFlow.Cli/
    CLI host using GSTFlow.Core directly

flutter_app/
    Flutter presentation application

gstflow_native/
    Flutter package_ffi package and generated Dart bindings

Why use a C# export project?

The engine remains F#.

The small C# project exists only because NativeAOT has clear support for exporting static methods marked with "UnmanagedCallersOnly" and an explicit entry-point name.

The export project must contain no GST rules.

Permitted responsibilities:

- pointer validation;
- bounded byte copying;
- ABI-version checks;
- exception containment;
- response-buffer allocation;
- calling the F# engine;
- releasing native-owned buffers.

Forbidden responsibilities:

- GST calculations;
- GSTIN validation;
- tax classification;
- verdict interpretation;
- canonical serialization alternatives;
- independent error rules.

The C# layer is an adapter, not a second engine.

---

3. Use one coarse-grained JSON boundary

Do not expose F# records, unions, lists, options or decimals as C structs.

Expose only UTF-8 requests and UTF-8 responses.

Recommended operations:

gstflow_abi_version
gstflow_engine_info
gstflow_validate_json
gstflow_verify_pack
gstflow_buffer_free

Avoid one FFI call per field or rule.

The intended call pattern is:

Complete document
    ↓ one FFI call
Complete canonical verdict

The serialization cost is minor compared with the architectural stability gained.

---

4. Correct C ABI

Create a public header:

#ifndef GSTFLOW_NATIVE_H
#define GSTFLOW_NATIVE_H

#include <stdint.h>
#include <stddef.h>

#ifdef _WIN32
#define GSTFLOW_EXPORT __declspec(dllexport)
#else
#define GSTFLOW_EXPORT __attribute__((visibility("default")))
#endif

#ifdef __cplusplus
extern "C" {
#endif

typedef struct GstFlowBuffer {
    uint8_t* data;
    size_t length;
} GstFlowBuffer;

typedef enum GstFlowStatus {
    GSTFLOW_OK = 0,
    GSTFLOW_INVALID_ARGUMENT = 1,
    GSTFLOW_INVALID_REQUEST = 2,
    GSTFLOW_ABI_MISMATCH = 3,
    GSTFLOW_INTERNAL_ERROR = 4
} GstFlowStatus;

GSTFLOW_EXPORT uint32_t gstflow_abi_version(void);

GSTFLOW_EXPORT GstFlowStatus gstflow_engine_info(
    GstFlowBuffer* output);

GSTFLOW_EXPORT GstFlowStatus gstflow_validate_json(
    const uint8_t* input,
    size_t input_length,
    GstFlowBuffer* output);

GSTFLOW_EXPORT GstFlowStatus gstflow_verify_pack(
    const uint8_t* input,
    size_t input_length,
    GstFlowBuffer* output);

GSTFLOW_EXPORT void gstflow_buffer_free(
    GstFlowBuffer buffer);

#ifdef __cplusplus
}
#endif

#endif

ABI rules

1. All payloads are UTF-8 bytes.
2. Every buffer includes an explicit length.
3. No operation relies on null termination.
4. NativeAOT allocates response buffers.
5. NativeAOT frees response buffers.
6. Dart allocates and frees request buffers.
7. No managed pointer escapes.
8. No exception crosses the ABI.
9. Output is initialized to "{NULL, 0}" before processing.
10. Maximum input and output sizes are enforced.
11. Every function returns a stable numeric status.
12. ABI version and protocol version are independent.

---

5. Request protocol

Use a versioned request envelope:

{
  "protocolVersion": "gstflow-native/1",
  "operation": "validateInvoice",
  "requestId": "2c85b793-8d16-48e4-a054-a939adb47abb",
  "payload": {
    "invoice": {}
  }
}

Response:

{
  "protocolVersion": "gstflow-native/1",
  "requestId": "2c85b793-8d16-48e4-a054-a939adb47abb",
  "engine": {
    "version": "0.1.0",
    "commit": "abc123",
    "canonFlowVersion": "0.1.0",
    "rulePackId": "gst-india",
    "rulePackVersion": "2026.07",
    "rulePackDigest": "sha256:..."
  },
  "result": {
    "status": "Pass",
    "verdictEnvelope": {}
  }
}

The F# engine owns response serialization.

Dart must not reconstruct or recanonicalize the verdict envelope.

---

6. Safe NativeAOT export host

Illustrative C# façade:

using System.Runtime.CompilerServices;
using System.Runtime.InteropServices;

namespace GSTFlow.NativeExports;

[StructLayout(LayoutKind.Sequential)]
public unsafe struct GstFlowBuffer
{
    public byte* Data;
    public nuint Length;
}

public static unsafe class NativeApi
{
    private const uint AbiVersion = 1;
    private const int MaxInputBytes = 25 * 1024 * 1024;

    [UnmanagedCallersOnly(EntryPoint = "gstflow_abi_version")]
    public static uint GetAbiVersion() => AbiVersion;

    [UnmanagedCallersOnly(EntryPoint = "gstflow_validate_json")]
    public static int ValidateJson(
        byte* input,
        nuint inputLength,
        GstFlowBuffer* output)
    {
        if (output is null)
            return 1;

        output->Data = null;
        output->Length = 0;

        if (input is null || inputLength == 0)
            return 1;

        if (inputLength > MaxInputBytes)
            return 2;

        try
        {
            var request = new ReadOnlySpan<byte>(
                input,
                checked((int)inputLength));

            // Ordinary managed call into the authoritative F# engine.
            byte[] response =
                GSTFlow.Core.NativeFacade.validateUtf8(request.ToArray());

            if (response.Length == 0)
                return 4;

            byte* responsePtr =
                (byte*)NativeMemory.Alloc(
                    checked((nuint)response.Length));

            response.CopyTo(
                new Span<byte>(responsePtr, response.Length));

            output->Data = responsePtr;
            output->Length = checked((nuint)response.Length);

            return 0;
        }
        catch
        {
            // Detailed exception is logged locally under diagnostic policy.
            // Do not leak raw exception messages across the ABI.
            return 4;
        }
    }

    [UnmanagedCallersOnly(EntryPoint = "gstflow_buffer_free")]
    public static void FreeBuffer(GstFlowBuffer buffer)
    {
        if (buffer.Data is not null)
            NativeMemory.Free(buffer.Data);
    }
}

NativeAOT exports must use unmanaged-compatible entry points, and methods marked with "UnmanagedCallersOnly" are the supported mechanism.

Important detail

Do not construct error JSON manually in the C# layer.

The engine should expose a managed function that always returns one of:

ValidationResponse.Success
ValidationResponse.Rejected
ValidationResponse.InternalFailure

All three should pass through the same canonical encoder.

The C ABI status reports transport-level success or failure.

The JSON envelope reports domain-level success or failure.

These must not be confused.

---

7. NativeAOT project settings

Initial desktop shared-library project:

<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <PublishAot>true</PublishAot>
    <NativeLib>Shared</NativeLib>
    <InvariantGlobalization>true</InvariantGlobalization>
    <IlcGenerateCompleteTypeMetadata>false</IlcGenerateCompleteTypeMetadata>

    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    <WarningsAsErrors>$(WarningsAsErrors);IL2XXX;IL3XXX</WarningsAsErrors>
  </PropertyGroup>

  <ItemGroup>
    <ProjectReference Include="..\GSTFlow.Core\GSTFlow.Core.fsproj" />
  </ItemGroup>

</Project>

The exact framework version should match the repository’s supported SDK.

NativeAOT applications are self-contained and compiled ahead of time, but trimming and static analysis constraints mean reflection-heavy libraries require special attention.

Release law

AOT warnings = build failure
Trimming warnings = build failure
Interop analyzer warnings = build failure

Do not suppress warnings globally.

Resolve or narrowly document every suppression.

---

8. Flutter integration

Flutter currently recommends creating native integration through:

flutter create --template=package_ffi gstflow_native

The modern "package_ffi" model uses build hooks to configure and bundle native artifacts.

Use "ffigen" to generate Dart bindings from "gstflow_native.h".

Do not maintain function signatures twice by hand.

Dart struct

final class GstFlowBuffer extends Struct {
  external Pointer<Uint8> data;

  @Size()
  external int length;
}

Dart service boundary

abstract interface class GstFlowEngine {
  EngineInfo get engineInfo;

  Future<ValidationResponse> validateInvoice(
    String requestJson,
  );
}

Implementation:

final class NativeGstFlowEngine implements GstFlowEngine {
  NativeGstFlowEngine(this._bindings);

  final GstFlowNativeBindings _bindings;

  @override
  Future<ValidationResponse> validateInvoice(
    String requestJson,
  ) async {
    return Isolate.run(() => _validateSync(requestJson));
  }

  ValidationResponse _validateSync(String requestJson) {
    final requestBytes = utf8.encode(requestJson);

    final input = calloc<Uint8>(requestBytes.length);
    final output = calloc<GstFlowBuffer>();

    try {
      input.asTypedList(requestBytes.length).setAll(0, requestBytes);

      final status = _bindings.gstflow_validate_json(
        input,
        requestBytes.length,
        output,
      );

      if (status != GstFlowStatus.ok) {
        throw NativeEngineException(status);
      }

      final length = output.ref.length;
      final data = output.ref.data;

      if (data == nullptr || length == 0) {
        throw const NativeEngineException.emptyResponse();
      }

      final responseBytes =
          Uint8List.fromList(data.asTypedList(length));

      final responseJson =
          const Utf8Decoder(allowMalformed: false)
              .convert(responseBytes);

      return ValidationResponse.fromJson(
        jsonDecode(responseJson) as Map<String, Object?>,
      );
    } finally {
      if (output.ref.data != nullptr) {
        _bindings.gstflow_buffer_free(output.ref);
      }

      calloc.free(output);
      calloc.free(input);
    }
  }
}

Why run it in an isolate?

Invoice validation may include:

- parsing;
- hashing;
- large item lists;
- rule evaluation;
- evidence construction.

Calling it on the Flutter UI isolate risks visible stalls.

Keep FFI calls coarse-grained and execute substantial work away from the UI isolate.

Dart FFI supports C interoperability for Dart Native platforms, including mobile and desktop applications.

---

9. Platform strategy

Phase 1: Windows x64

Prove:

- DLL loads in debug and release;
- ABI version resolves;
- canonical fixture validates;
- CLI and FFI output bytes match;
- repeated calls do not leak;
- malformed requests do not crash.

Phase 2: Android ARM64

Package:

android-arm64/libgstflow_native.so

Prove on:

- one physical ARM64 device;
- one emulator architecture;
- release APK or AAB;
- cold start;
- background isolate;
- repeated app suspend/resume.

Phase 3: Linux x64

Useful for:

- CI;
- deterministic native-library tests;
- low-cost packaging validation.

Phase 4: macOS ARM64

Verify:

- hardened runtime;
- code signing;
- library discovery;
- notarized release packaging.

Phase 5: iOS ARM64

Do not treat iOS as “copy a ".dylib" into the app.”

.NET supports NativeAOT libraries for iOS-like platforms, but the documented approach uses Apple-compatible framework packaging and has additional interop constraints.

Produce and consume:

GSTFlowNative.framework

Then validate:

- device build;
- release signing;
- App Store archive;
- static analysis;
- no dynamic code assumptions;
- no unsupported JIT/reflection behavior.

---

10. Keep server integration direct

Do not make the .NET server call its own F# engine through FFI.

Use:

ASP.NET Core
    ↓ managed reference
GSTFlow.Core

Use the native library only for non-.NET hosts such as Flutter.

This avoids unnecessary serialization and native-memory boundaries on the server.

The release relationship is still exact because all hosts consume the same versioned engine source and rule pack.

---

11. Browser architecture

"dart:ffi" does not provide the ordinary Flutter Web execution path. It is intended for Dart Native applications.

Therefore retain:

Browser playground
    ↓
Fable JavaScript or .NET WebAssembly

The browser remains a translated or WebAssembly target and still requires agreement against the native oracle.

Recommended law:

[
NativeOracle(x)

BrowserTarget(x)
]

for every supported fixture.

Compare:

- verdict state;
- issue IDs;
- ordering;
- canonical response bytes;
- hash;
- engine and rule-pack identities.

The native Flutter clients no longer need Fable-Dart agreement tests, but the browser still needs cross-runtime conformance.

---

12. Testing model

Layer A — Pure engine tests

Run directly against "GSTFlow.Core".

Cover:

- parsing;
- GSTIN;
- invoice mathematics;
- interstate/intrastate logic;
- tax rates;
- effective dates;
- unsupported cases;
- canonical serialization;
- hashing.

Layer B — Native export tests

Load the produced library from a non-.NET test harness.

Verify:

Input fixture
→ C ABI
→ exact expected bytes

Include:

- zero-length request;
- null input;
- oversized input;
- invalid UTF-8;
- malformed JSON;
- valid Unicode;
- 10,000 sequential calls;
- concurrent calls;
- repeated allocation/free;
- forced domain failure;
- internal error handling.

Layer C — CLI versus FFI conformance

same request bytes
    ├── CLI
    └── Native library

expected:
CLI response bytes = FFI response bytes

This checks the wrapper and packaging.

It does not compare two independently implemented engines.

Layer D — Flutter integration tests

Verify that Flutter:

- loads the correct ABI;
- rejects incompatible versions;
- passes exact UTF-8 byte lengths;
- frees native output;
- displays every status correctly;
- does not treat transport failure as GST failure;
- does not recompute tax outcomes;
- ships the intended release library.

Layer E — Browser agreement

Native oracle response
=
Browser response

This remains the true cross-runtime agreement suite.

---

13. Concurrency model

Start with a stateless native API.

Every call should receive all necessary input:

document
engine options
rule-pack identity or loaded-pack reference

Avoid global mutable state.

Preferred:

pure request
→ pure response

If pack loading becomes expensive, introduce opaque handles only later:

gstflow_engine_create
gstflow_engine_validate
gstflow_engine_destroy

But handles introduce:

- lifetime management;
- concurrency rules;
- stale references;
- process-state complexity.

Do not begin there.

First prove the stateless buffer API.

---

14. Rule-pack strategy

The Flutter shell must never silently supply different GST knowledge from the CLI.

Every verdict must contain:

engine version
CanonFlow version
GSTFlow version
rule-pack ID
rule-pack version
rule-pack digest
ABI version
protocol version

Choose one of two models:

Embedded pack

The signed pack is embedded into the native library.

Advantages:

- easiest deployment;
- no missing files;
- exact release identity.

Disadvantages:

- updating knowledge requires shipping a new native artifact.

External signed pack

Flutter supplies signed pack bytes to the engine.

Advantages:

- update rules without replacing the full binary;
- historical replay.

Disadvantages:

- more lifecycle and storage work.

Recommended progression:

Preview release:
    embedded signed pack

Later:
    externally loadable signed packs

---

15. Security rules

The native ABI is part of the attack surface.

Enforce:

- bounded request size;
- checked integer conversions;
- strict UTF-8;
- no arbitrary file path access through the ABI;
- no callbacks initially;
- no managed pointer handles;
- no raw exception text;
- deterministic memory ownership;
- zero native network access;
- fuzz testing;
- dependency scanning;
- signed release artifacts.

Microsoft’s current native-interop guidance emphasizes deliberate interop declarations and safe marshalling practices.

For diagnostic logging:

default:
    no private document content

optional developer mode:
    request ID, engine identity and error code only

Do not log invoice JSON by default.

---

16. What “One Engine” now means

The correct constitutional definition is:

«All native clients execute the same authoritative F# implementation of GST laws through a versioned C ABI. No native presentation shell reimplements, translates or interprets those laws.»

Formally:

[
\forall p \in NativePlatforms:
Verdict_p(x)

GSTFlowCore(x)
]

The CLI and Flutter do not each contain a verdict algorithm.

They are hosts around the same core.

For the browser:

[
Verdict_{browser}(x)
\stackrel{tests}{=}
Verdict_{native}(x)
]

This is stronger and more honest than claiming one physical binary across all surfaces.

---

17. Migration from Fable-Dart

Do not delete the current Fable-Dart experiment immediately.

Use this migration sequence:

Step 1

Freeze Fable-Dart features.

No new business functionality.

Step 2

Create "GSTFlow.NativeExports".

Export:

gstflow_abi_version
gstflow_engine_info
gstflow_validate_json
gstflow_buffer_free

Step 3

Build Windows x64 shared library.

Step 4

Create "gstflow_native" with Flutter’s "package_ffi" template.

Step 5

Generate bindings from the header using "ffigen".

Step 6

Run identical fixture through:

CLI
Fable-Dart prototype
NativeAOT FFI

Do not require Fable-Dart to remain permanently equal; use it only as transition evidence.

Step 7

Build Android ARM64.

Step 8

Prove memory safety and release packaging.

Step 9

Switch Flutter UI to "NativeGstFlowEngine".

Step 10

Remove generated Dart engine files.

Retain only any independent Fable browser target.

---

18. Acceptance gates

The pivot is complete only when all gates pass.

Correctness

- CLI and native library produce identical canonical bytes.
- "System.Decimal" calculations remain entirely inside the F# engine.
- SHA-256 is computed by the engine.
- Flutter contains no tax calculations.
- Every verdict records engine and pack identity.

ABI safety

- explicit byte lengths;
- strict UTF-8;
- no uncaught exceptions;
- no null-termination assumption;
- matching allocator/free functions;
- no leaks after soak tests;
- no invalid memory access under fuzzing.

Platform

- Windows x64 release works.
- Android ARM64 release works.
- library packaging is automated.
- wrong ABI version is rejected.
- missing library produces a controlled startup error.

Build

- clean-clone reproducible;
- pinned SDKs;
- NativeAOT warnings treated as errors;
- generated bindings checked for drift;
- native artifacts carry commit and version metadata.

Trust

- browser agreement remains green;
- external contact proof exists;
- documentation does not say “same physical binary”;
- documentation does not claim agreement tests are unnecessary;
- known limitations are published.

---

19. Recommended initial scope

Do not begin with every platform.

Approve this first milestone:

GSTFlow Native Engine Preview 0.1

Platforms:
    Windows x64
    Android ARM64

Operations:
    engine info
    validate invoice JSON
    free output buffer

Knowledge:
    one embedded signed GST pack

Evidence:
    CLI/FFI byte equality
    malformed-input fuzzing
    10,000-call leak test
    physical Android-device proof

After that succeeds:

0.2:
    Linux x64
    macOS ARM64

0.3:
    iOS ARM64 framework

0.4:
    external signed packs
    batch validation

---

Final assessment

Flutter plus NativeAOT FFI is the strongest architecture currently identified for GSTFlow’s native clients.

It combines:

- Flutter’s user-interface reach;
- F#’s domain modelling;
- real .NET decimal arithmetic;
- the existing CanonFlow verification kernel;
- one canonical serializer;
- one hashing implementation;
- a small auditable interop boundary.

Its main cost is not semantic drift—it is native packaging and ABI engineering.

That is a preferable trade:

[
\text{Explicit packaging complexity}
<
\text{Hidden verdict divergence}
]

The decision should therefore be:

«Adopt Flutter + NativeAOT FFI as GSTFlow’s native-client architecture. Begin with a stateless, length-aware, UTF-8 C ABI on Windows x64 and Android ARM64. Keep the browser as an independently compiled agreement target. Declare the architecture selected—not the Trinity complete—until the ABI, builds, conformance tests and external-use evidence pass.»My fresh take: this architecture also clarifies CanonFlow’s role. CanonFlow should produce portable, versioned verdict engines; Flutter, CLI, server and browser are hosts. That distinction is cleaner and more reusable than framing CanonFlow primarily as a transpiler.




