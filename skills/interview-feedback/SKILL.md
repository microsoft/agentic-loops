---
name: interview-feedback
description: Draft coding-interview feedback from meeting transcripts with the human's original question, TL;DR, and ten-bullet prompt. Use transcript text only. Do not select a hiring verdict.
---

# Interview feedback

Use this skill when the human asks for feedback from one or more interview meetings.

## Get the transcript

1. Get each meeting link or the transcript text from the human.
2. Verify that each transcript belongs to the requested meeting and candidate.
3. Read the complete coding portion of each transcript. Exclude system design.
4. Use transcript text only. Do not read attachments, screenshots, resumes, or captured code.
   Do not use chat messages or recording metadata as a substitute for transcript text.
5. If a transcript route returns an access error, report the error for that route. Do not infer
   that all transcript routes are unavailable. Use another permitted transcript route if available.
6. If the coding transcript is still missing or incomplete, identify the affected meeting and
   the missing coverage. Report the blocker. Do not write feedback from excerpts unless the human
   explicitly approves that limit. Do not repeat searches that return only metadata.

Keep the evidence for each interview separate. Do not copy facts from another candidate.

## Run the prompt

Run the original prompt below on each interview's coding transcript. The supplied TL;DR is the
human's assessment, not a request for you to make a hiring decision. Do not replace it with a
verdict of your own or ask the human to justify it.

```text
put it in the following format in a scratch md
1. question
2. TL;DR NO HIRE.
candidate is not a coder. maybe a good script guy that can put things together. depending on other rounds, context in overall space may be a HIRE.
3. 10 bullet points of the discussion (highlight how he wanted to jump to coding without forming a mental model. with difficulty and whole lot of hints he wasn't able to)

there was a design part but leave that out. start from the coding.
```

Use the question, name, and pronouns from the current interview. If the human supplies a different
assessment or changes the prompt, use that instruction instead.

Keep the human's assessment separate from the transcript facts. Do not invent dialogue, hints,
test results, strengths, or failures to support it. If the transcript does not show a requested
detail, state that limit. Do not infer a final coding outcome from a partial transcript.

## Write the feedback

Write one Markdown file per interview under `scratch/interview-feedback/`, unless the human
requests a different location. Use these three sections:

1. **Question:** the coding question from that transcript.
2. **TL;DR:** the human's supplied assessment.
3. **Discussion:** ten distinct, transcript-supported bullet points, in discussion order.

Include a short transcript source link and state the coverage used. Do not invent timestamps or
pad the discussion to reach ten points. If the evidence is insufficient, report the missing input.

Keep transcripts and feedback private. Do not put candidate data in this skill. Do not commit the
output or post it to a chat or an interview system. Give the human links to the saved files and
state any retrieval limit.
