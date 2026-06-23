# Apple Music Playlist History

Git-backed history of Apple Music playlist states.

## Files

- `Favorite Songs.tsv`: Apple Music's built-in `Favorite Songs` playlist.

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

`~/repos/script-applemusichistory`

From this directory, run:

```zsh
npm run snapshot
```

That command delegates to:

```zsh
/Users/jacob/repos/script-applemusichistory/scripts/capture_current_snapshot.zsh --playlist-repo .
```

The default blank-commit policy is `auto`: if the playlist has not changed, create a blank commit only when the last commit is more than 24 hours old. That records that the state remained stable without creating junk commits in quick succession.

Override it when needed:

```zsh
npm run snapshot -- --blank=always
npm run snapshot -- --blank=never
```

## Notes

- Current snapshots read only the local Music.app library through Apple's `iTunesLibrary.framework`.
- Time Machine and Backblaze recovery work belongs in the script repo, not here.
- Commits should use `git cic` or include the configured Codex co-author trailer.
