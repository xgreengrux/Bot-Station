<div align="center">

# Bot Station

**A free online social-deduction game for 4–10 players. It runs in your browser: no install, no accounts.**

One of the bots is the saboteur. Do your tasks, find the traitor, vote them out.

[![Play now](https://img.shields.io/badge/%E2%96%B6%20Play%20now-xgreengrux.github.io%2FBot--Station-4f8cff?style=for-the-badge)](https://xgreengrux.github.io/Bot-Station/)

![4–10 players](https://img.shields.io/badge/players-4%E2%80%9310-6c7bff) ![No install](https://img.shields.io/badge/install-none-2ea44f) ![License: MIT](https://img.shields.io/badge/license-MIT-blue)

<img src="assets/gameplay.jpg" alt="A technician in the Greenhouse, with a limited field of view and a task list" width="780">

</div>

## Play in 30 seconds

1. Open **<https://xgreengrux.github.io/Bot-Station/>**, type a name and press **Create party**.
2. Send friends the 5-letter code or press **Copy invite link**. They open it, type a name and press **Join**.
3. When 4 or more players are in, the party leader presses **START GAME**.

No friends online right now? Press **Browse public parties** on the title screen to see every open public party, live, and join one.

## How a round works

- **Technicians** finish tasks around the station. **Saboteurs** (1–3, you choose) blend in, eliminate technicians, sabotage systems and hide in the vents.
- Found a body, or suspicious about someone? **Report** it or hit the **emergency button**. Everyone meets, chats, and votes someone out.
- **Technicians win** by finishing every task or ejecting every saboteur. **Saboteurs win** by matching the technicians in number, or by letting a sabotage run out the clock.
- Sabotages: a reactor meltdown (two players must hold both panels), lights out, jammed comms, and locked doors.
- Eliminated technicians become ghosts: they float through walls and keep finishing tasks, but they can't vote.
- The station has nine rooms, an admin table, a map, vents, and 15 tasks (wiring, door codes, thrusters, scanners, power diversion and more).

## Controls

| Action | Keyboard | Touch |
| --- | --- | --- |
| Move | `W` `A` `S` `D` or arrow keys | drag |
| Use / do a task | `E` or `Space` | **USE** button |
| Report a body | `R` | **REPORT** button |
| Eliminate (saboteur) | `Q` | button |
| Vent (saboteur) | `V` | button |
| Sabotage (saboteur) | `X` | button |
| Map | `M` or `Tab` | **MAP** button |

## Screenshots

<table>
  <tr>
    <td><img src="assets/title.jpg" alt="Title screen: create a party, join with a code, or browse public parties"><br><sub>Title screen</sub></td>
    <td><img src="assets/browse-parties.jpg" alt="Browse public parties: live list of parties with their players and a search box"><br><sub>Browse public parties</sub></td>
  </tr>
  <tr>
    <td><img src="assets/task.jpg" alt="A task mini-game"><br><sub>Task mini-game</sub></td>
    <td><img src="assets/meeting.jpg" alt="Emergency meeting with chat and voting"><br><sub>Meeting and chat</sub></td>
  </tr>
  <tr>
    <td><img src="assets/eject.jpg" alt="The ejection screen after a vote"><br><sub>The vote result</sub></td>
    <td><img src="assets/mobile.jpg" alt="The game on a phone" height="260"><br><sub>On a phone</sub></td>
  </tr>
</table>

## Find a party (server selector)

**Browse public parties** shows every active public party and updates by itself: its code, leader, status (in lobby, starting, in game or full), player count and everyone's name and robot.

- **Search for people.** Type a name, or just the start of one, and only the parties those players are in stay on screen, with the matching players highlighted. Separate several names with commas and choose **Any name** (at least one of them is in the party) or **All names** (every one of them is). The start of a party code works as a search too.
- **Join** puts you into a party that is open. Parties that are full or already playing are listed so you can see where your friends are, but they can't be joined.
- New parties are **public by default**. Tick **Private party** on the title screen, or use the Public / Private chip in the lobby (leader only), to keep a party out of the list. Private parties never contact the list service at all; friends can still join with the code or invite link.

## Host settings

The party leader can change 16 settings in the lobby: number of saboteurs, eliminate and sabotage cooldowns, player speed, technician and saboteur vision, emergency meetings and their cooldown, discussion and voting time, how many common, short and long tasks each technician gets, when the task bar updates, whether ejects are confirmed, and anonymous votes. There are 12 robot colours and 6 hats.

## FAQ

**Why can't I start a game alone?** The game needs at least 4 players in the same party, and there are no computer players. Bring friends, or watch the public list for a party that is filling up.

**Do I need an account?** No. Pick a name and play.

**Does it work on a phone?** Yes: there are touch controls, and the layout adapts to small screens. The party leader should keep their tab open and in the foreground.

**What happens if someone refreshes or loses connection?** They can rejoin the same party as the same player within about 25 seconds. If the leader closes their tab, the party ends.

**The party list says "Offline".** The list uses public servers that can be down or blocked by a school or work network. You can still join a friend with their party code.

**Connection problems on some networks?** The free setup uses public STUN only. Strict NATs and some school or corporate networks need a TURN server: set `BS.PEER_OPTIONS` (see `js/net.js`) to your own PeerServer or TURN configuration.

## Privacy

- There is no account, no tracking and no database. Names exist only for the length of a party.
- The game is peer-to-peer (WebRTC), like a video call. Players connect to the party leader's browser, which means **the leader's browser can see the network addresses of the people who join**. Only join parties run by people you trust.
- **Public parties are visible to everyone.** Their code, leader and player names are published on free public MQTT brokers so they can be listed, and anyone who opens the list (or those brokers) can read them. Anyone can also publish fake entries. Use **Private party** to stay out of the list, and don't use your real name.

## How it works

There is no game server to run. The party leader's browser **is** the server: it keeps the authoritative game state (map, tasks, roles, cooldowns, votes) and the other players are thin clients that send their inputs and draw what the leader tells them.

- **Connections:** [PeerJS](https://peerjs.com/) (WebRTC data channels). Peer IDs are `botstation-<CODE>`, so a party code is all a friend needs.
- **Party list:** the leader publishes a small *retained* [MQTT](https://mqtt.org/) message (code, status, players) to two free public brokers, refreshes it every 25 seconds, and registers a *last will* so the broker removes the entry if the leader's connection dies. The browse screen subscribes to `botstation/v1/parties/+` and merges both brokers. `js/mqtt.js` is a small MQTT 3.1.1 client written for this project; `js/registry.js` holds the list logic (sanitising, expiry, search). To use your own broker, set `BS.REGISTRY_BROKERS` before the game scripts load.
- **Frontend:** plain JavaScript and canvas, no framework and no runtime dependencies other than PeerJS (loaded from a CDN). The build step just concatenates everything into one self-contained `index.html`.
- **Same-computer mode:** add `?local` to the URL to swap the network for a same-computer transport (several tabs of one browser, including the party list). The tests use it. `?broker=ws://localhost:PORT/mqtt` points the party list at a local test broker (loopback addresses only).

## Run it yourself

Fork or copy this repo, then in Settings → Pages choose **Deploy from a branch** → `main` / `(root)`. `index.html` is fully self-contained, so that is all it takes.

To work on it locally:

```bash
git clone https://github.com/xgreengrux/Bot-Station.git
cd Bot-Station
node build.js                      # rebuilds index.html from index.src.html, css/ and js/
python3 -m http.server 8000        # then open http://localhost:8000/?local in a few tabs
```

Tests (run them one after another; the browser tests are heavy for small machines):

```bash
node test/map.test.js && node test/sim.test.js            # map and game rules
node test/mqtt.test.js && node test/registry.test.js      # MQTT client and party list logic
npm i playwright                                          # for the browser tests below
node test/e2e.js; node test/e2e2.js                       # whole games in browser tabs
node test/peer.e2e.js                                     # PeerJS code path (stand-in for its API)
node test/e2e3.js; node test/e2e4.js                      # party browser: same-computer and MQTT paths
```

The real public brokers and the real PeerJS cloud can't be reached from a typical test sandbox, so those two network paths are tested against the protocol specs and stand-ins (`test/mockbroker.js`, `test/fakepeer.js`).

### Project layout

```
index.html         the built, self-contained game (what GitHub Pages serves)
index.src.html     HTML shell used by build.js
css/style.css      all styles
js/shared.js       constants: map, tasks, settings
js/host*.js        authoritative game logic (runs in the leader's browser)
js/net.js          PeerJS and same-computer transports
js/mqtt.js         tiny MQTT-over-WebSocket client
js/registry.js     public party list: publish, watch, search
js/render.js       canvas drawing        js/minigames.js   task mini-games
js/ui_*.js         menus, party browser, in-game HUD
js/client.js       app state and flow    js/play.js        boot
test/              unit tests and Playwright browser tests
assets/            screenshots and the social-preview image
```

## Contributing

Bug reports, ideas and pull requests are welcome. Good places to start: a new map, more task mini-games, sounds, translations, a better mobile layout, or documentation for hosting your own TURN server. Please run the unit tests before opening a pull request.

## Credits and disclaimer

Bot Station is an independent hobby project inspired by the social-deduction genre (Mafia, Werewolf, and games in that family such as Among Us). The robots, art, map and name are original and drawn in code. It is not affiliated with, endorsed by or sponsored by Innersloth LLC or any other game's publisher.

## License

[MIT](LICENSE)
