# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
<!-- save_to_watchlist() should follow the project's naming convention. Compare with add_to_collection() — the pattern here is verb_to_noun. Please rename to add_to_watchlist() and update all call sites. -->
**What I did:** I queried the AI assistant to identify all instances where "save_to_watchlist" could be found and replace that with "add_to_watchlist".
**How I verified:** I was able to review its output because before making this request I ran grep commands to find all locations where "save_to_watchlist" was used in the codebase. Then, after prompting, I asked it to show me where it made the changes and cross-validated the changes where made where needed.

## Comment 2 — Deduplication
<!-- What happens if a user calls this with a film that's already on their watchlist? The current implementation would add a duplicate entry. Please handle this case. -->
**What I did:** I added a new `AlreadyInWatchlistError` exception and a check in `add_to_watchlist()` that queries for an existing entry with the same user_id and film_id before inserting, raising the error if one is found. I followed the same pattern that `add_to_collection()` already uses so the two features stay consistent.
**How I verified:** I asked the AI assistant to validate the handling, and it ran the service against an in-memory database which tried to add the same film twice. The first add succeeded, the second raised `AlreadyInWatchlistError`, and the entry count stayed at 1, confirming no duplicate was created.

## Comment 3 — Missing test
<!-- Please add a test for the case where film_id doesn't exist in the database. Look at the existing tests in test_collection.py — the pattern is there. -->
**What I did:** I added `test_add_to_watchlist_nonexistent_film_raises` in test_collection.py, right under the equivalent collection test. It reuses the same `app` and `sample_user` fixtures and asserts that calling `add_to_watchlist()` with a film_id that isn't in the database raises `FilmNotFoundError`, mirroring the existing pattern.
**How I verified:** I ran the test with pytest and it passed, confirming the nonexistent-film case raises `FilmNotFoundError` rather than a database integrity error.

## Comment 4 — Default visibility
<!-- I notice watchlists default to public=True. We don't have a documented decision on default visibility for user lists. Before I can approve this, I need you to add a note to your PR description explaining your reasoning. I want to make sure we're being intentional here, not just inheriting a default. -->
**My position:** It makes sense to use `public=True` as default for watchlists.
**Reasoning:** The purpose of the application is to be community based, sharing ratings and interests with your fellow cineophiles. I think to embrace that the default should be public and users can make the change if need.
**Tradeoff acknowledged:** This may come at some inconvenience for users that hope to keep this aspect of their film interest private or be more of a spectator than community participant.

## Comment 5 — Sort order
<!-- I'd prefer watchlists to default to "date added" order rather than alphabetical. Most users want to see what they added recently. I'm open to discussion if you see it differently — but let's make a decision and document it. -->
**My position:** We should change the order to ascending from most recently added.
**Reasoning:** This makes sense since often times user want to refer back to films they added recently. Alphabetical is almost akin to being randomized.
**Engagement with reviewer's point:** I agree with the reviewers point about sort order.

## Comment 6 — Rebase
<!-- A refactor merged to main that changed film IDs from integers to UUIDs. Your watchlist code still references integer IDs. Please rebase on main and update accordingly. -->
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->