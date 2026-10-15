# Awesome AI Character Cards

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated collection of specifications, libraries, editors, parsers, converters, tools, examples, and developer resources for AI character cards.

AI character cards are structured representations of fictional characters used by role-playing applications and LLM frontends.

Instead of storing a character as one giant prompt, character-card formats can represent information such as:

```text
Name
Description
Personality
Scenario
Greeting
Example dialogue
System prompt
Creator metadata
Lorebooks
Alternate greetings
Extensions
Assets
```

Character cards can then be imported, exported, shared, edited, validated, and used across compatible AI applications.

This repository focuses specifically on the **technical ecosystem around character cards** rather than providing a directory of AI companion apps.

---

## Contents

- Specifications
- Schemas & Types
- Parsers & Libraries
- Character Card Editors
- Converters
- Validators & Inspectors
- Character Card Frontends
- Character Card Directories
- Lorebooks & World Info
- Prompt Architecture
- Developer Guides
- Example Cards
- Related Projects
- Contributing

---

# Specifications

## Character Card V1

The original Tavern-style character format.

A typical V1 card contains:

```ts
type TavernCard = {
  name: string;
  description: string;
  personality: string;
  scenario: string;
  first_mes: string;
  mes_example: string;
};
```

V1 established many of the fields still used by modern character-chat applications.

**Specification:**

https://github.com/malfoyslastname/character-card-spec-v2/blob/main/spec_v1.md

---

## Character Card V2

The widely implemented successor to the original Tavern Card specification.

V2 introduced additional fields such as:

```text
creator_notes
system_prompt
post_history_instructions
alternate_greetings
character_book
tags
creator
character_version
extensions
```

It also formalized character books/lorebooks as part of the character package.

**Specification:**

https://github.com/malfoyslastname/character-card-spec-v2

---

## Character Card V3

Character Card V3 extends previous formats with additional capabilities for modern role-playing applications.

It introduces concepts intended to support richer character packages, assets, and extensibility.

**Specification:**

https://github.com/kwaroran/character-card-spec-v3

---

## Unified Character Card Specification

A community-maintained reference documenting Character Card V1 through V3 together.

Useful when implementing import/export support and understanding differences between versions.

https://github.com/BasedInn/Unified-CharacterCard-Specification

---

# Schemas & Types

Typed representations make implementing character cards significantly easier.

## Character Card V2 TypeScript Structure

The V2 specification itself provides the canonical field structure and expected behavior.

https://github.com/malfoyslastname/character-card-spec-v2

Useful fields include:

```ts
interface CharacterCard {
  spec: string;
  spec_version: string;
  data: {
    name: string;
    description: string;
    personality: string;
    scenario: string;
    first_mes: string;
    mes_example: string;
    creator_notes: string;
    system_prompt: string;
    post_history_instructions: string;
    alternate_greetings: string[];
    tags: string[];
  };
}
```

---

## Character Card Spec V2 Documentation

Expanded community documentation covering the V2 data structures and individual fields.

https://github.com/bradennapier/character-cards-v2

Useful for implementers who want a more explanatory reference than the raw specification.

---

# Parsers & Libraries

Character-card parsers typically need to support both:

```text
JSON cards
PNG cards containing embedded JSON
```

Depending on the specification version, implementations may additionally support richer containers.

When choosing a parser, check:

- supported specification versions;
- PNG metadata support;
- lorebook support;
- extension preservation;
- TypeScript/Python support;
- unknown-field preservation.

---

# Character Card Editors

Character card editors let creators modify structured character data without editing JSON manually.

A useful editor should expose fields separately rather than presenting one giant prompt box.

Typical sections include:

```text
Identity
Description
Personality
Scenario
Greeting
Example messages
System prompt
Alternate greetings
Lorebook
Creator notes
Metadata
```

When evaluating an editor, check whether importing and exporting a card preserves unknown extension fields.

---

# Character Card Frontends

## SillyTavern

https://github.com/SillyTavern/SillyTavern

One of the most widely used open-source character-roleplay frontends.

Its ecosystem includes:

- character cards;
- personas;
- lorebooks / World Info;
- group chats;
- prompt customization;
- RAG;
- extensions;
- multiple model backends.

Because of its large ecosystem, compatibility with SillyTavern character formats is useful for developers building character-oriented applications.

---

## RisuAI

https://github.com/kwaroran/RisuAI

Open-source character-chat frontend supporting modern character-card concepts.

The project's author also maintains the Character Card V3 specification.

---

## TavernAI

https://github.com/TavernAI/TavernAI

An influential early character-chat frontend whose card conventions helped establish the ecosystem from which later formats evolved.

---

## KoboldAI / KoboldAI Lite

https://github.com/KoboldAI/KoboldAI-Client

LLM storytelling and role-playing ecosystem commonly used alongside character-oriented interfaces.

---

# Character Card Directories

Character-card directories allow creators to publish and exchange reusable characters.

These can be useful to developers for studying:

- real-world card structures;
- commonly populated fields;
- creator conventions;
- lorebook usage;
- interoperability problems.

When consuming public cards programmatically, always check the directory's API terms and redistribution policies.

---

# Lorebooks & World Info

Character identity and world knowledge are different problems.

A character might contain:

```text
Name: Alice

Personality:
Curious, sarcastic, analytical
```

while a lorebook might contain:

```text
Location: Aerilon

Aerilon is a floating city powered by
crystalline engines discovered 600 years ago.
```

Separating these allows the application to retrieve world information only when it becomes relevant.

---

## SillyTavern World Info

https://docs.sillytavern.app/usage/core-concepts/worldinfo/

A practical implementation of dynamically injecting world and lore information into character conversations.

Useful for developers implementing keyword-triggered or similarity-based lore retrieval.

---

# Prompt Architecture

Simply concatenating every character field is rarely the ideal implementation.

A character-chat context might instead be assembled as:

```text
SYSTEM INSTRUCTIONS
        ↓
CHARACTER IDENTITY
        ↓
CHARACTER PERSONALITY
        ↓
SCENARIO
        ↓
RELEVANT LORE
        ↓
RELEVANT MEMORIES
        ↓
RECENT CONVERSATION
        ↓
USER MESSAGE
```

Character cards solve only part of this pipeline.

They describe the character.

Applications must still decide:

- which fields enter the model context;
- in what order;
- with which priority;
- how much token budget each section receives;
- how memories are inserted;
- how lore is retrieved;
- how example dialogue influences style.

---

# Character Fields Explained

Different fields should serve different purposes.

For example:

### Description

Defines who the character fundamentally is.

```text
Alice is a detective working in near-future Tokyo.
```

### Personality

Defines behavioral tendencies.

```text
Observant, sarcastic, calm under pressure.
```

### Backstory

Provides historical information that should influence behavior.

```text
Alice grew up in...
```

### Greeting

Controls how a new conversation begins.

### Example Dialogue

Demonstrates how the character should speak rather than merely describing the style.

### Speaking Style

Can explicitly define characteristics such as:

```text
short sentences
dry humor
formal vocabulary
frequent rhetorical questions
```

### Memories

Should generally represent information learned or retained through interaction rather than static character identity.

A practical overview of separating these fields is available here:

**AI Character Creator — Character Fields and Structure**

https://yourcrush.ai/blog/ai-character-creator

The guide covers character descriptions, backstories, greetings, traits, speaking styles, and memories as separate parts of a character definition.

---

# Memory Is Not a Character Card

One implementation mistake is treating memory as simply another static character field.

Consider:

```text
CHARACTER CARD

Name: Alice
Occupation: Detective
Personality: Sarcastic
```

versus:

```text
MEMORY

The user told Alice yesterday
that their interview is Friday.
```

The first should remain stable.

The second evolves continuously.

A production character system may therefore use:

```text
Character Card
     +
Conversation Summary
     +
Retrieved Memories
     +
Lorebook
     +
Recent Messages
     ↓
LLM
```

rather than continuously rewriting the character card.

---

# Example Character Card

A simplified implementation might look like:

```json
{
  "spec": "chara_card_v2",
  "spec_version": "2.0",
  "data": {
    "name": "Alice",
    "description": "Alice is a private investigator...",
    "personality": "Observant, sarcastic and analytical.",
    "scenario": "The user has hired Alice to investigate...",
    "first_mes": "You're late. Sit down.",
    "mes_example": "{{user}}: Did you find anything?\n{{char}}: More than I wanted to.",
    "creator_notes": "",
    "system_prompt": "",
    "post_history_instructions": "",
    "alternate_greetings": [],
    "tags": [
      "detective",
      "original-character"
    ],
    "creator": "example",
    "character_version": "1.0",
    "extensions": {}
  }
}
```

Applications should consult the relevant specification rather than relying on this simplified example for strict interoperability.

---

# Import / Export Checklist

If you're implementing character-card support, test more than the happy path.

### Import

Check:

- JSON cards;
- PNG-embedded cards;
- missing optional fields;
- empty strings;
- unknown extension fields;
- large lorebooks;
- Unicode names;
- multiline example dialogue;
- alternate greetings.

### Export

Verify that:

- imported fields are preserved;
- extensions aren't silently deleted;
- Unicode survives round-tripping;
- PNG metadata is correctly encoded;
- lorebooks remain intact.

A useful test is:

```text
Card A
  ↓
Import
  ↓
Internal representation
  ↓
Export
  ↓
Card B
```

Then compare A and B programmatically.

---

# Character Card Validation

A validator can check:

```text
Specification identifier
Specification version
Required fields
Field types
Lorebook structure
Malformed JSON
PNG metadata
Extension namespaces
Invalid placeholders
```

For developer tooling, returning precise field-level errors is significantly more useful than simply returning:

```text
Invalid character card.
```

Prefer errors such as:

```text
data.alternate_greetings expected array<string>
but received string.
```

---

# Interoperability

Interoperability is the main reason structured character formats matter.

Without a shared representation:

```text
Platform A character
        X
Platform B character
```

With an agreed card format:

```text
Character Card
   ↙       ↓       ↘
App A    App B    App C
```

Perfect portability is still difficult because applications may interpret prompts, lorebooks and extensions differently.

A valid card can therefore produce different behavior across different models and frontends.

---

# Security Considerations

Character cards are user-generated data.

Applications importing them should treat their contents as untrusted.

Consider:

- malformed metadata;
- extremely large embedded payloads;
- unexpected HTML;
- URLs embedded in extensions;
- prompt injection;
- unsupported file formats;
- decompression limits;
- potentially malicious assets.

Never assume that a character-card file is safe merely because it appears to be a PNG.

---

# Developer Guides

## Character Card V2 Specification

https://github.com/malfoyslastname/character-card-spec-v2

The primary technical reference for V2-compatible implementations.

## Character Card V3 Specification

https://github.com/kwaroran/character-card-spec-v3

Useful for developers following the newer generation of the format.

## Character Card V2 Documentation

https://github.com/bradennapier/character-cards-v2

Field-by-field community documentation and implementation notes.

## Designing AI Character Fields

https://yourcrush.ai/blog/ai-character-creator

Practical explanation of the roles played by character descriptions, backstories, traits, greetings, speaking styles and memories.

---

# Example Cards

## teknium1/character-cards

https://github.com/teknium1/character-cards

A public collection of example character-card-style definitions used for AI role-playing.

Examples are useful for testing parsers because real cards frequently contain edge cases that synthetic test fixtures miss.

---

# Related Projects

## Awesome AI Character Engineering

A broader collection covering the engineering behind persistent AI characters, including:

- memory;
- models;
- RAG;
- voice;
- avatars;
- evaluation;
- inference;
- character architecture.

Use that list for the overall AI-character stack.

Use this repository specifically for the **character-card ecosystem and interoperability layer**.

---

# Contributing

Contributions are welcome.

Good additions include:

- character-card specifications;
- parsing libraries;
- editors;
- validators;
- converters;
- migration tools;
- interoperability test suites;
- developer documentation;
- character-card-compatible open-source applications.

Please avoid:

- generic AI companion directories;
- affiliate comparison pages;
- unrelated prompt collections;
- abandoned projects with no historical relevance;
- links added primarily for SEO.

Commercial projects may be included when they provide a genuinely useful technical resource, tool, specification, or implementation reference.

If you maintain or work on a submitted resource, disclose that relationship in the pull request.

Each contribution should answer:

> What problem does this resource solve for someone implementing or working with AI character cards?

---

# License

CC0 1.0 Universal

To the extent possible under law, contributors waive copyright and related rights to this curated list.
