# Markdown to Speech Synthesis (TTS) Preprocessing Specification

[TL;DR: Give me the prompt!](#1-zero-shot-refactoring-prompt)

## Overview

Text-to-speech (TTS) engines—whether classical concatenative systems, neural parametric pipelines, or autoregressive audio models—are optimized for natural conversational prosody. When supplied with raw Markdown, TTS engines struggle with visual syntax: ASCII directory trees, code blocks, tabular layouts, mathematical operators, raw acronyms, and markdown structural glyphs generate acoustic artifacts, unnatural pauses, or phonetic misinterpretations.

Achieving high-fidelity audio output from technical Markdown requires an intermediate Large Language Model (LLM) refactoring pass. This pipeline normalizes visual artifacts into auditory cues, flattens non-linear layouts into spatial narratives, and injects prosodic cadence before audio generation.

```
+-------------------+      +----------------------+      +-------------------+
|   Raw Technical   | ---> |  LLM Normalization   | ---> |  Engine-Optimized | ---> [ Neural TTS ]
|     Markdown      |      |   (Ruleset Engine)   |      |  Plaintext / SSML |
+-------------------+      +----------------------+      +-------------------+

```

---

## 1. Zero-Shot Refactoring Prompt

To automate the transformation, pipe the raw Markdown through a fast, instruction-tuned LLM using the structured prompt scaffolding below:

```plaintext
You are an expert audio-engineering transcription agent specializing in pre-processing technical documentation for neural Text-to-Speech (TTS) engines and human listening comprehension.

Your task is to refactor the provided "Markdown Plaintext" into a continuous, high-fidelity spoken-word script adhering strictly to the "TTS Transformation Ruleset".

[INPUT_MARKDOWN]
{{PASTE_RAW_MARKDOWN_HERE}}
[/INPUT_MARKDOWN]

[TTS_TRANSFORMATION_RULESET]
1. Numerical Normalization: Spell out all numbers, decimals, metrics, and semver strings (e.g., "11" -> "eleven", "0.48" -> "zero point four eight", "v5" -> "version five").
2. Acoustic Waypoints: Convert visual headings (#, ##) and dividers into explicit auditory signposts (e.g., "Section Heading:", "Key Takeaway:").
3. Glyph & Symbol Verbalization: Translate all icons, arrows, and operators into descriptive context (e.g., "⏮" -> "Previous Track icon", "->" -> "which maps to").
4. Spatial Narrative Flattening: Flatten ASCII trees, graphs, and indented directory structures into top-down, relational narrative walkthroughs.
5. Code & Schema Deconstruction: Never recite brackets, delimiters, or raw syntax. Translate schemas and code snippets into functional plain English descriptions.
6. Phonetic Decoupling: Space out unpronounceable acronyms, CLI utilities, and file extensions (e.g., ".msi" -> "dot M S I", "yt-dlp" -> "Y T hyphen D L P").
7. Prosodic Cadence: Use deliberate punctuation (commas, ellipses, colons) to enforce natural engine breaths and pacing.
8. SSML Injection (Optional): If targeting an SSML-compliant runtime, utilize <say-as>, <break>, <sub alias>, and <prosody> tags for strict phonetic control.
[/TTS_TRANSFORMATION_RULESET]

Output ONLY the refactored, TTS-ready content. Do not include introductory remarks, meta-announcements, or post-generation analysis.

```

---

## 2. Audio-First Transformation Ruleset

### 2.1 Numerical Normalization & Lexical Expansion

Neural voices frequently stumble across unformatted numbers, treating them as digit sequences or misreading decimals and versions as dates or IP addresses.

* **Integers and Ordinals:** Convert digit strings to spelled words (`"24"` $\rightarrow$ `"twenty-four"`, `"1st"` $\rightarrow$ `"first"`).
* **Decimals and Ratios:** Verbalize point structures explicitly (`"0.96 s"` $\rightarrow$ `"zero point nine six seconds"`).
* **Software Versions:** Prevent semantic collapse on dot-notation (`"v2.5.1"` $\rightarrow$ `"version two point five point one"`).
* **Hex / Hashes / Hashes / Keys:** Group long alphanumeric hashes into spaced sets of two or three characters, or describe their generation algorithm rather than reading raw entropy strings.

### 2.2 Structural Waypoints & Visual Demarcation

Visual hierarchy must be translated into explicit auditory breadcrumbs.

* **Heading Flattening:** Replace `#`, `##`, and `###` with verbal navigational anchors: `"Section: ..."`, `"Technical Specification: ..."`, or `"Subtopic: ..."`.
* **Bullet Points and Enumerations:** Replace raw symbols (`*`, `-`) with sequential spoken indicators: `"First point:"`, `"Subsequent step:"`, `"Finally:"`.
* **Callouts and Blockquotes:** Translate visual warning boxes (`> [!WARNING]`) into tone-setting vocal transitions: `"Important caveat:"`, `"Warning notice:"`.

### 2.3 Symbol, Operator, and Glyph Verbalization

Non-alphanumeric characters lack consistent phonetic tokenization across TTS engines.

* **Directional Vectors:** Verbalize logic gates and flow indicators (`"A -> B"` $\rightarrow$ `"A, which routes to B"`).
* **Transport and Media Controls:** Replace Unicode glyphs with descriptive labels (`"⏮"` $\rightarrow$ `"Previous Track icon"`, `"▶"` $\rightarrow$ `"Play icon"`).
* **Comparative Operators:** Transcribe mathematical syntax into speech (`"score >= 0.5"` $\rightarrow$ `"score is greater than or equal to zero point five"`).

### 2.4 Spatial Graph & ASCII Tree Deconstruction

Visual representations of directory structures, class diagrams, or network graphs are entirely illegible when read token-by-token.

* **Narrative Traversal:** Deconstruct file trees hierarchically from root to leaves.
* **Relational Clustering:** Group files by domain and intent rather than printing raw filepaths.
* *Bad:* `"Pipe-dash-src-slash-app-dot-cs"`
* *Good:* `"Under the root directory, the source folder contains three primary subprojects: the core engine, the desktop user interface, and the installer package."`



### 2.5 Syntactic Deconstruction (Code, SQL, & DDL)

Raw code recited aloud imposes extreme cognitive load on the listener.

* **Abstract Syntax Translation:** Convert raw SQL tables, JSON objects, or C# signatures into descriptive sentences explaining their structural intent.
* *Raw:* `detections(id PK, video_guid FK, start_ms, end_ms, peak_conf)`
* *TTS Refactor:* `"The detections table records each event with an internal primary key, references the parent video globally unique identifier, and indexes the start time, end time, and peak confidence score."`


* **Command-Line Invocations:** Explain the flags instead of reciting raw arguments (`"yt-dlp -U"` $\rightarrow$ `"run the update command for Y T hyphen D L P"`).

### 2.6 Phonetic Decoupling & Acronym Management

* **Extensions:** Prefix file extensions with the spoken word "dot" and space out the letters (`".msi"` $\rightarrow$ `"dot M S I"`, `".tar.gz"` $\rightarrow$ `"dot tar dot G Z"`).
* **Hyphenated Utilities:** Separate compound developer tool names (`"yt-dlp"` $\rightarrow$ `"Y T hyphen D L P"`).
* **All-Caps Acronyms:** Isolate letter groups prone to being pronounced as pseudo-words (`"WPF"` $\rightarrow$ `"W P F"`, `"GUID"` $\rightarrow$ `"G U I D"` or `"globally unique identifier"`).

### 2.7 Prosodic Cadence & Pause Sculpting

TTS engines modulate pitch and pause length based strictly on punctuation.

* **Micro-Pauses:** Inject commas between conceptual clauses to force engine breath cycles.
* **Inter-Section Breaks:** Use colons followed by line breaks to signal structural transitions.
* **Ellipses for Suspension:** Utilize ellipses (`...`) when a deliberate trailing pause is needed before revealing a critical technical result.

---

## 3. Advanced Engine Control: SSML Integration

For runtimes supporting Speech Synthesis Markup Language (SSML)—such as Azure Cognitive Speech, Google Cloud Text-to-Speech, or AWS Polly—the refactoring stage can replace heuristic punctuation with direct XML control elements.

### Recommended SSML Primitives

| SSML Tag | Technical Utility | Implementation Example |
| --- | --- | --- |
| `<break time="..." />` | Precise millisecond pause insertion between sections. | `<break time="750ms"/>` |
| `<say-as interpret-as="...">` | Guarantees exact pronunciation of acronyms, characters, and ordinals. | `<say-as interpret-as="characters">WPF</say-as>` |
| `<sub alias="...">` | In-line textual replacement for complex formulas or abbreviations. | `<sub alias="Windows Presentation Foundation">WPF</sub>` |
| `<prosody rate="..." pitch="...">` | Modulates speaking speed and pitch to distinguish metadata from body text. | `<prosody rate="-5%" pitch="+2st">Technical note:</prosody>` |
| `<phoneme alphabet="..." ph="...">` | Defines exact International Phonetic Alphabet (IPA) mouth shapes for obscure tools. | `<phoneme alphabet="ipa" ph="ˈæk.sɪs">AXIS</phoneme>` |
| `<mark name="..." />` | Emits runtime event hooks to synchronize audio streams with UI subtitles/highlights. | `<mark name="step_two"/>` |

### Production SSML Ingestion Template

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <prosody rate="0%" pitch="0%">
    <emphasis level="strong">Section: Detection Pipeline Architecture.</emphasis>
    <break time="500ms"/>

    Step one: Extract raw audio streams using 
    <sub alias="F F mpeg">ffmpeg</sub>. 
    The audio is normalized to a sixteen kilohertz mono stream.
    <break time="300ms"/>

    Step two: Forward the frame buffers directly into the inference engine running 
    <say-as interpret-as="characters">ONNX</say-as> Runtime.
    <mark name="checkpoint_inference_ready"/>
    
    <prosody rate="-5%" pitch="-1st">
      Note: Peak memory consumption remains strictly bounded under sixty-four megabytes.
    </prosody>
  </prosody>
</speak>

```

---

## 4. Pipeline Integration Checklist

Before sending any refactored text payload to your audio generation endpoint, ensure the pipeline validates:

* [ ] **No bare code fences:** Triple backticks (`````) stripped and converted to conversational explanations.
* [ ] **Zero unexpanded digits:** No raw numerals left to the arbitrary judgment of the TTS model.
* [ ] **Linear narrative flow:** Complex ASCII layouts completely converted into top-down descriptions.
* [ ] **Acronym clarity:** Critical developer tools and abbreviations spaced out or bound to `<say-as>` tags.
* [ ] **Auditory landmarks established:** Explicit navigational cues present for each section heading.