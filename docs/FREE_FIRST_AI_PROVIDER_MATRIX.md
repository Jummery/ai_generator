# Personal AI Animation Factory — Free-First AI Provider Matrix

## 1. Objective

The project is a personal AI animation production tool. Its primary objective is to generate videos for personal viewing at the lowest practical cost, with free usage preferred whenever the provider terms allow it.

This document is a routing baseline, not a permanent price list. Provider pricing, quotas, model availability, and commercial terms change frequently and must be revalidated before production use.

## 2. Cost policy

Priority order:

1. Free and explicitly permitted personal-use quota
2. Free web workflow when automation is not required
3. Low-cost API
4. Premium API only for visually important shots

The system must never assume that a free web UI means a free API.

## 3. Provider matrix

| Capability | Candidate | Current assessment | Role in MVP |
|---|---|---|---|
| LLM / story | DeepSeek API | Very low token cost; current V4 pricing is documented by DeepSeek | Primary story/script/planning engine |
| LLM / fallback | Gemini API | Free-tier text usage exists, but quotas vary by model and plan | Secondary story/QC engine |
| Image | Gemini image models | Do not classify as free API without checking the active project quota; current documentation distinguishes image models from free text usage | Optional image provider |
| Video | Vidu API | Paid credits; useful for selected image-to-video/reference-to-video shots, not a free baseline | Premium/important shots |
| TTS | Google Cloud TTS | Standard voices include a monthly free character allowance; billing must be enabled | Primary low-cost TTS candidate |
| TTS / voice | ElevenLabs | Free plan exists with monthly credits, but commercial rights and API limits differ by plan | Voice-quality fallback / testing |
| Render | FFmpeg | Free software | Primary rendering/compositing engine |

## 4. Important current findings

### DeepSeek

DeepSeek currently documents `deepseek-v4-flash` and `deepseek-v4-pro`. V4 Flash is priced very low for text generation, making it suitable for story planning, episode scripts, shot descriptions, prompt generation, and QC reasoning. The project should still treat pricing as dynamic and query the provider configuration rather than hard-code prices.

### Gemini

Gemini 2.5 Flash-Lite is positioned by Google as a cost-efficient, high-volume model and supports structured outputs and function calling. Free-tier availability depends on the model and quota configuration. Native image generation must not be treated as automatically free: image-generation models have separate quota/billing behavior.

### Vidu

Vidu API is explicitly credit-based. Current documentation lists 1 credit = $0.005. Examples include Q3 Turbo reference-to-video at $0.02/sec for 540p and $0.05/sec for 720p, with off-peak pricing lower. The API currently requires adding credits before normal use. Therefore Vidu belongs in the paid/important-shot path rather than the free path.

### Google Cloud TTS

Google Cloud Text-to-Speech currently documents a monthly free allowance for standard voices and requires billing to be enabled. This makes it a strong candidate for narration/dialogue in the MVP, subject to account-region availability and current quota.

### ElevenLabs

ElevenLabs currently lists a $0 Free plan with 10k monthly credits and TTS, sound effects, music and other creation capabilities. The free plan has licensing limitations, so the project must distinguish personal viewing from commercial publishing before using generated assets publicly.

## 5. MVP generation policy

The MVP should use three generation modes:

### Mode A — FREE / NEAR-FREE

```text
Story / Script      -> low-cost or free LLM
Character Bible     -> LLM
Storyboard          -> LLM
Character reference -> lowest-cost permitted image workflow
Motion              -> pan / zoom / parallax / cuts
Voice               -> free-tier TTS
Music/SFX            -> permitted free assets or provider free quota
Subtitle            -> local processing
Composition         -> FFmpeg
```

This mode should be the default.

### Mode B — LOW COST

Use a paid API only when the free workflow cannot produce an acceptable result:

```text
Reference image
    -> image-to-video provider
    -> 3–8 second shot
    -> QC
```

### Mode C — PREMIUM SHOT

Reserve higher-cost generation for:

- episode opening hook
- protagonist introduction
- major transformation
- combat/climax
- emotional close-up
- major reveal
- episode ending hook

The majority of shots should remain in Mode A or Mode B.

## 6. Personal-use architecture

```text
                    Personal Project
                          |
                    Story / Script
                          |
                    Storyboard / Shot
                          |
                     Free-First Router
                          |
             +------------+------------+
             |                         |
        Free / Near-Free           Paid Shot
             |                         |
       TTS / Motion / Image       Vidu / other
             |                         |
             +------------+------------+
                          |
                       Assets
                          |
                        FFmpeg
                          |
                         MP4
```

## 7. Provider abstraction rules

Even though this is a personal project, providers must be abstracted by capability rather than hard-coded into the story pipeline.

Required logical capabilities:

- `llm.text_generation`
- `image.text_to_image`
- `image.reference_to_image`
- `video.image_to_video`
- `video.reference_to_video`
- `tts.text_to_speech`
- `audio.music`
- `audio.sfx`
- `media.compose`

A provider may implement one or more capabilities.

## 8. Free-first router rules

1. Prefer a provider with an active free quota.
2. Verify quota before starting a batch.
3. Do not spend paid credits on draft generations.
4. Generate low-resolution previews before final renders.
5. Reuse accepted character/location reference assets.
6. Regenerate only the failed shot instead of the episode.
7. Escalate to paid video generation only after the shot is approved.
8. Keep a per-shot cost record.
9. Stop the batch when the configured personal budget is reached.
10. Never bypass provider limits, CAPTCHA, payment controls, or access restrictions.

## 9. Recommended first production pipeline

For the first real episode:

```text
1. LLM -> 2-minute script
2. LLM -> 15–30 shots
3. Image/reference workflow -> character and scene images
4. Motion Comic -> ordinary shots
5. TTS -> dialogue/narration
6. Paid image-to-video -> only 2–5 key shots if necessary
7. Subtitle generation
8. FFmpeg composition
9. Local review
10. Record cost and quality
```

The first goal is not cinematic quality. The first goal is proving that one complete episode can be generated repeatedly at a predictable personal cost.

## 10. Decision

`ai_generator` should therefore remain a **personal orchestration layer**, not a model company.

The next engineering/product milestone is not another provider list. It is the **Episode Cost Budget + Generation Policy**, which decides before generation which shots are free, low-cost, or premium and prevents accidental spending.