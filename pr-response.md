# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename

**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `service/watchlist_service`.py to match the verb_to_noun convention used by `add_to_collection()`. Updated the import and call site in `routes/watchlist.py.

**How I verified:** Confirmed the app starts without ImportError after the rename, and the route still calls the function correctly.

## Comment 2 — Deduplication

**What I did:** Added in AlreadyInWatchlistError exception and a check in `add_to_watchlist()` that queries for an existing WatchlistEntry before creating a new one, mirroring the pattern in `add_to_collection()`.

**How I verified:** In flask shell, called `add_to_watchlist()` with the same user_id/film_id twice. First call succeeded and returned a new entry. Second call raised AlreadyInWatchlistError with the message "Film '1' is already in this user's watchlist" - confirmed via try/except.

## Comment 3 — Missing test

**What I did:** Created tests/test_watchlist.py with test_add_to_watchlist_nonexistent_film_raises, modeled directly on test_add_to_collection_nonexistent_film_raises from test_collection.py. Copied the app and sample_user fixtures into the new file since pytest fixtures aren't shared across files without a conftest.py.

**How I verified:** Ran pytest tests/test_watchlist.py -v - passed. Then ran the full suite (pytest tests/ -v) - all 5 tests passed, confirming nothing else broke

## Comment 4 — Default visibility

**My position:** Watchlists should default to `public=False` (private), not `public=True`.

**Reasoning:** Addin a film to a watchlist is a personal act - "I want to watch this" - not a social anouncement. Most users add films for their own tracking, not because they intend to broadcast their taste. Defaulting to private respects that intent and lets a user opt into sharing deliberately, rather than assuming visibility they never chose.

**Tradeoff acknowledged:** CineLog's stated goal is a social, community-driven disovery experience, and public-by-default would serve that direclty - friends could see what others want to watch without any manual step. But public by default risks quietly shaping what people add in the first place: if a list is visible from the start, users may avoid adding guilty-pleasure or low-rated films they'd otherwise track honestly, which undermines the authenticity of the discovery feature it's meant to support. A useful analogy is a video store's "staff picks" shelf - it worked because it was a deliberate, intentional act of curation and sharing, not someone's private rental history made public by default. Private-by-default preserves that same option: a user can still choose to share a list, but starts from a place of control rather than exposure.

## Comment 5 — Sort order

**My position:** Sort by date_added, newest first - matching @dev-lead's suggestion.

**Reasoning:** Alphabetical order buries everything equally, regardless of how recently it was added - a title starting with 'A' sits at the top forever, while something added yesterday can be invisible if it stars with 'Z.' There's no signal of freshness at all. Sorting newest-first matches how people actually use a watchlist: a film addeed last week is usually there because something just reminded the user of it - a trailer, a friend's recommendation, a mod - and that recency is exactly why it's more likely to be what they're in the mood for tonight. The list should surface what's top of mind, not force a user to scan alphabetically for it.

**Engagement with reviewer's point:** I agree directly with @dev-lead's reasoning - "most users wantt to see what they added recently" matches how I'd actually use this feature myself. I'll note one nuance: there's a separate, real use case for browsing older, buried entries when a user is feeling picky rather than decisive - but that's a browsing behavior, not a default-view behavior. Newest-first should remain the default; a future alphabetical or "surprise me" view could serve that browsing case without changing what a user sees on first load.

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->