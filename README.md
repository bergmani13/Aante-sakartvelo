# აანთე საქართველო

Georgian interactive map with a quiz and a Georgian hangman game. Correct quiz answers and completed hangman words earn energy. During testing each light costs one energy. Standard lights last 24 hours, premium-story lights last seven days, and permanent lights do not expire; all three types are also available for emigrants.

## Run

Install the locked dependencies with the Sites installer, generate migrations with `npm run db:generate` when the schema changes, build with the Sites build helper, and apply pending SQL migrations to local D1 before running the managed preview. `npm test` runs the Node test suite. Map libraries and Georgian fonts are bundled; map tiles require internet access.

## Data and access

- Shared lights, stories and the anonymous quiz session are stored in D1. A random HttpOnly browser cookie identifies the anonymous session; if that cookie is lost, the person's quiz progress cannot be recovered automatically. Active shared lights are visible on the map; expired entries remain in the owner history and admin panel.
- The server assigns expiry from its own creation timestamp, filters expired lights from the public map, allows relighting expired locations, chooses questions, checks answers and atomically spends energy. Answer keys are not shipped in the public asset bundle. A question is not repeated in a session.
- Hangman rounds and their guesses are stored against the same anonymous session. A completed word awards one energy exactly once. Each word permits six mistakes and two free, one-use hints; words are selected randomly without repeats until the current bank is exhausted. The answer is kept on the server until the round is finished. Quiz answer statistics remain separate from hangman results.
- New hangman rounds use the medium/hard bank. Previously started rounds remain playable even when their word is retired from new selection. Sound effects are synthesized locally after a user gesture; the mute preference is stored in the browser and the game works without audio support.
- A signed-in owner identified by the hosted identity headers and the `ADMIN_EMAIL` runtime secret can list, search and remove lights, and can reset the shared lights after a separate confirmation. Public visitors cannot use these administrative endpoints. Map reset does not erase participants' quiz progress.
- Older browser-local data is not automatically published. The owner can explicitly import it from the browser where it is stored. Import publishes any old titles, stories, authors and coordinates to everyone, and merges the old quiz progress with that anonymous browser session. A duplicate map position is skipped; imported quiz counts can include attempts made both before and after the upgrade.
- Previously published or legacy-imported lights retain their former no-expiry behavior. Premium and permanent stories, authors, titles and precise selected coordinates are public while the light is active. The UI discloses this before publication. Shared light changes are refreshed while the page is open; this is polling, not instant streaming.
- Lights are fetched in pages; the map currently renders every light, so add spatial clustering and more efficient updates before expecting very large collections. Premium and permanent lights are planned as paid features, but currently all three cost one energy and no actual payment or checkout exists. The app does not offer account-based recovery or a self-service import of JSON backups.
- Shared lights and anonymous quiz history use separate local and hosted D1 databases. An administrator's map reset is permanent; export/restore of the hosted database is an operational task, not yet a UI feature. Do not run destructive operations as part of a deployment.

## Security and limitations

All writes require a browser session, an origin check and a custom request header. Individual admin endpoints additionally check the hosted authenticated identity against the configured owner email. This depends on Sites dispatch supplying trusted identity headers. Public use is not yet protected by a dedicated network-level rate limiter or automated content moderation. The quiz is educational and noncompetitive, not a high-stakes assessment.

The satellite imagery is aerial, not live. Georgia's Natural Earth outline is generalized and not suitable for cadastral or border determination. Search combines a small local city list with explicitly submitted Photon/OpenStreetMap address queries, scoped to the Georgian outline. Submitted search text is sent from the visitor's browser to Photon. Coverage of villages, streets and house numbers varies, and the public Photon service may be unavailable or rate-limited; confirm exact addresses with the map. New standard, premium and emigrant lights must be within the existing generalized outline. Historical emigrant lights abroad are preserved. Tile availability and provider usage terms apply.

## Source attribution

- MapLibre GL JS: https://maplibre.org/ (BSD-3-Clause)
- Leaflet: https://leafletjs.com/ (BSD-2-Clause)
- Imagery and streets: Esri, Maxar, Earthstar Geographics, HERE, Garmin, OpenStreetMap contributors.
- Georgia geometry: Natural Earth public-domain data via datasets/geo-countries.
- Address search: Photon (https://photon.komoot.io/) using OpenStreetMap contributor data (https://www.openstreetmap.org/copyright).

- Noto Sans Georgian, Noto Serif Georgian and subsetted Noto Sans Latin and Noto Symbols: SIL Open Font License 1.1; see `public/fonts/NOTO-LICENSE.txt`.
