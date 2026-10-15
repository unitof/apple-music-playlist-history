# Apple Music Playlist History

Git-backed history of Apple Music playlist states.

## Files

- `Favorite Songs.tsv`: Apple Music's built-in `Favorite Songs` playlist.
- `Noggy.tsv`: the user's `Noggy` playlist (tracking starts October 15, 2026).
- `playlists.json`: ID/name/folder/kind index of all locally exposed playlists.
- `playlists/<persistent_id>.tsv`: each additional playlist, including empty playlists/folders. IDs avoid duplicate-name and rename collisions.

Each playlist should stay as a single file. Historical changes should be represented by commits to that file, not by date-stamped copies.

## Commit Date Convention

- Commit author date: the date represented by the playlist state.
- Commit date: the date the state was actually committed to this repo.

For example, the recovered Time Machine state uses an author date of `2026-03-06T11:49:14-05:00`, while its commit date is when it was added to this repo.

## Known States

- `2026-03-06`: recovered from Time Machine snapshot `2026-03-06-114914`.
- `2026-06-22`: exported from the current local Music.app library through `iTunesLibrary.framework`.

## Capture A New Current Snapshot

This repo intentionally keeps capture logic out of the data history. The implementation lives in:

`~/repos/scripts/apple-music-history`

From this directory, run:

```zsh
npm run snapshot
```

That command delegates to:

```zsh
/Users/jacob/repos/scripts/apple-music-history/scripts/capture_current_snapshot.zsh \
  --playlist-repo . --all-playlists
```

The default blank-commit policy is `auto`: if the playlist has not changed, create a blank commit only when the last commit is more than 24 hours old. That records that the state remained stable without creating junk commits in quick succession.

Override it when needed:

```zsh
npm run snapshot -- --blank=always
npm run snapshot -- --blank=never
```

## Notes

- Daily snapshots run via the native `com.unitof.apple-music-history` LaunchAgent at 10:00 AM local time, with scripts and installation instructions in `~/repos/scripts/apple-music-history`. No LLM is used. It captures all locally exposed playlists; automatic public uploads of all playlists were explicitly approved October 15, 2026. It does not force Apple cloud resync.
- The checkout lives at `~/repos/apple-music-playlist-history`. The similarly named Drive item is a Finder alias, not a shell directory.
- Capture commits locally. Run `git push` afterward to sync GitHub.
- All playlists exposed by iTunesLibrary are exported without modifying Music.app. Empty playlists, folders, duplicates, order, and repeats are preserved. Smart playlist rule definitions and cloud playlists not exposed locally are not captured. The whole export must validate before files change. Removed playlists disappear from current files/index but remain in Git history; dates record observations, not exact edits. One empty commit records a successful unchanged run.
- October 11, 2026 preflight found all persistent IDs changed since June. Exact title/artist/album multiset comparison showed 486 to 487 songs: only Tyler Lyle's Lakes of Canada (So, This is Christmas?) added, no removals. The cause of the ID change is unverified; ID-only diffs will misleadingly report every song replaced across this boundary.
- Current snapshots read only the local Music.app library through Apple's `iTunesLibrary.framework`.
- Time Machine and Backblaze recovery work belongs in the script repo, not here.
- Commits should use `git cic` or include the configured Codex co-author trailer.
