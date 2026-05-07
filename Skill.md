---
name: track-recommendation
description: Fetch DJ track recommendations from nexttrack.club. Use when the user asks what to play after (or before) a specific track, wants track suggestions, or asks for DJ set ideas. Input is a free-form track name / artist query.
---

The user wants track recommendations. Follow these steps:

1. **Extract the query** from the user's message — artist name, track title, or both. Use what they gave you verbatim; don't guess or expand it.

2. **Fetch recommendations** by calling:
   ```
   GET https://www.nexttrack.club/search?q=<url-encoded-query>
   ```
   Use the WebFetch tool. If the response is 404, tell the user the track wasn't found and ask them to try a different spelling. If 429, tell them the server is rate-limited and to try again shortly.

3. **Confirm the match** — the response includes a `matches` array and an `input` object. Compare `matches[0]` (has `artists` as a string and `title`) to what the user asked for. If it doesn't match, **call it out clearly** before showing results — state which track was used so they can correct it. Don't silently proceed with a wrong match.

4. **Present the results** in this format:

   **[Artist] – [Title]**
   *(use `input.artists` joined with ", " and `input.title` for the header)*

   **What to play after:**
   | # | Artist | Title | Genre | BPM | Key |
   |---|--------|-------|-------|-----|-----|
   For each entry in `recommendations`, fill the table. `artists` is an array — join with ", ". Omit BPM and Key columns if all values are empty. Omit rows where both artist and title are missing.

   **What to play before:** *(if `before` array is non-empty)*
   Same table format using `before` entries. `artists` is also an array here — join with ", ".

5. **Keep it concise.** Don't narrate the API call. Don't show raw JSON. If genre/label/BPM/key fields are empty for most tracks, omit those columns from the table entirely rather than showing blank cells.
