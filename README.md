# ♟️ Ban Chess Web

An experimental **online platform for playing Ban Chess**, a chess variant where players alternate between banning opponent moves and making their own moves. 

> 📖 **For complete game rules, see [RULES.md](./RULES.md)** - the canonical ruleset for Ban-Chess.

Built with:

- [Next.js 15.5.2 (App Router)](https://nextjs.org/) — UI & routing with React 19
- [@bezalel6/react-chessground](https://www.npmjs.com/package/@bezalel6/react-chessground) — interactive chessboard
- [ban-chess.ts v3.0.0](https://github.com/RNDev666/ban-chess.ts) — variant engine
- [WebSockets](https://www.npmjs.com/package/ws) — real-time sync between players
- [NextAuth.js](https://next-auth.js.org/) — optional authentication with Google & Lichess OAuth

This platform features anonymous-first gameplay inspired by Lichess, real-time multiplayer, sound effects, and extensive testing.

---

## 🚀 Features

### Core Gameplay
- Create solo practice games or find opponents online
- Real-time move + ban synchronization with WebSockets
- Interactive chessboard powered by **@bezalel6/react-chessground**
- Game logic enforced by **ban-chess.ts v3.0.0** with BCN (Ban Chess Notation) serialization
- Support for timed games with increment timers
- Proper ban phase mechanics - select opponent's pieces to ban their moves
- Game sound effects for moves, captures, bans, and game events

### Authentication & User Management
- **Anonymous-first experience**: Play immediately without any sign-in requirements
- **Automatic guest IDs**: Every visitor gets a persistent anonymous identity
- **Optional authentication**: Sign in with Google or Lichess OAuth if desired
- **Seamless transition**: Keep your games when transitioning from anonymous to authenticated
- **No barriers**: Following Lichess's philosophy - authentication never blocks gameplay

### User Experience
- Active game detection on home page with continue/resign options
- Clean resignation flow with inline split-button confirmation
- Resizable game board with persistent size preference
- Mobile-responsive design optimized for chess gameplay
- Clear game status indicators showing whose turn and what action (ban/move)
- Sound control settings with mute/volume options
- Comprehensive accessibility features

### Technical Features
- Efficient BCN serialization for moves/bans (50% bandwidth reduction)
- Automatic game cleanup after completion (2-second archival delay)
- WebSocket connection resilience with automatic reconnection
- Redis-backed game state for persistence across server restarts
- Separated concerns: WebSocket server for live games only
- Comprehensive E2E test coverage with Playwright
- TypeScript strict mode with complete type safety
- Performance monitoring and debugging tools

---

## 🛠️ Project Structure

```
2ban-2chess/
├─ app/                      # Next.js 15 App Router
│  ├─ page.tsx               # Landing page with active game detection
│  ├─ game/[id]/page.tsx     # Dynamic game board page
│  ├─ auth/                  # Authentication pages
│  │  ├─ signin/page.tsx     # Sign-in page with provider options
│  │  ├─ error/page.tsx      # Auth error handling
│  │  └─ logout/page.tsx     # Logout page
│  ├─ play/
│  │  ├─ local/page.tsx      # Solo practice mode
│  │  └─ online/page.tsx     # Online matchmaking
│  ├─ settings/page.tsx      # User settings and preferences
│  ├─ user/[username]/       # User profiles
│  └─ api/                   # API routes
│     ├─ auth/[...nextauth]/ # NextAuth.js configuration
│     ├─ health/             # Health check endpoint
│     └─ user/username/      # Username management
│
├─ components/
│  ├─ ChessBoard.tsx         # React Chessground wrapper with ban/move logic
│  ├─ GameClient.tsx         # Main game UI controller
│  ├─ ActiveGameCard.tsx     # Active game card with inline resign
│  ├─ AuthProvider.tsx       # Authentication wrapper component
│  ├─ SoundControl.tsx       # Sound settings and controls
│  ├─ auth/
│  │  ├─ SignInPanel.tsx     # Sign-in options panel
│  │  └─ withAuth.tsx        # Authentication HOC
│  └─ game/
│     ├─ GameSidebar.tsx     # Player info & move history
│     ├─ GameStatusPanel.tsx # Game state & controls
│     ├─ ResizableBoard.tsx  # Resizable chess board wrapper
│     └─ PlayerInfo.tsx      # Player information display
│
├─ hooks/
│  ├─ useGameState.tsx       # WebSocket game state management
│  └─ useGameTimer.tsx       # Game timer functionality
│
├─ lib/
│  ├─ game-types.ts          # TypeScript types & WebSocket messages
│  ├─ game-utils.ts          # Game permissions & role inference
│  ├─ fen.ts                 # FEN parsing with ban state support
│  ├─ auth.ts                # NextAuth.js configuration
│  ├─ sound-manager.ts       # Game sound management
│  └─ websocket-config.ts    # WebSocket connection configuration
│
├─ server/
│  ├─ ws-server.ts           # WebSocket server with Redis & game cleanup
│  ├─ redis.ts               # Redis connection and configuration
│  └─ services/
│     ├─ game-service.ts     # Game logic service
│     └─ timer-service.ts    # Timer management service
│
├─ contexts/
│  └─ WebSocketContext.tsx   # WebSocket connection & auth provider
│
├─ e2e/                      # Playwright E2E tests
│  ├─ auth.spec.ts           # Authentication flow tests
│  ├─ game.spec.ts           # Game functionality tests
│  ├─ multiplayer-flow.spec.ts # Multiplayer game tests
│  └─ accessibility.spec.ts  # Accessibility tests
│
├─ types/
│  ├─ auth.ts                # Authentication type definitions
│  └─ next-auth.d.ts         # NextAuth.js type extensions
│
└─ docs/                     # Documentation
   ├─ DEVELOPMENT.md         # Development guide
   ├─ ARCHITECTURE.md        # Architecture documentation
   ├─ DEPLOYMENT.md          # Production deployment guide
   ├─ API_REFERENCE.md       # API documentation
   ├─ OAUTH_SETUP.md         # OAuth configuration
   └─ ban-chess-api.md       # BCN format specifications
```

---

## ⚡ Quick Start

### 1. Clone & Install
```bash
git clone https://github.com/RNDev666/2ban-2chess.git
cd 2ban-2chess
npm install
```

### 2. Start Development Servers

**Recommended approach:** Run each server in a separate terminal for better log insight and debugging.

**Terminal 1: WebSocket Server**
```bash
npm run dev:ws
```
This starts the WebSocket server on **ws://localhost:3001** with hot reload.

**Terminal 2: Next.js Development Server**
```bash
npm run dev:next
```
This starts the Next.js app at [http://localhost:3000](http://localhost:3000).

**Alternative: Single Command** *(less optimal for debugging)*
```bash
npm run dev
```
Runs both servers concurrently, but logs are mixed and harder to analyze.

> 📖 **For detailed development setup, debugging tips, and best practices, see [docs/DEVELOPMENT.md](./docs/DEVELOPMENT.md)**

### 3. Authentication Setup (Completely Optional)

Authentication is never required to play. If you want to enable optional OAuth sign-in, set up environment variables:

```bash
# Google OAuth (optional)
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret

# Lichess OAuth (optional) 
LICHESS_CLIENT_ID=your_lichess_client_id

# NextAuth secret (required for production)
NEXTAUTH_SECRET=your_nextauth_secret

# WebSocket URL (auto-detected in development)
NEXT_PUBLIC_WEBSOCKET_URL=ws://localhost:3001
```

> 📖 **For detailed OAuth setup instructions, see [docs/OAUTH_SETUP.md](./docs/OAUTH_SETUP.md)**

### 4. Testing & Quality Assurance

```bash
# Run E2E tests with Playwright
npm run test

# Type checking
npm run type-check

# Linting
npm run lint

# Run both type-check and lint
npm run analyze
```

---

## 🎮 Usage

1. **Visit the app**: Open [http://localhost:3000](http://localhost:3000)
2. **Start playing immediately**: You're automatically assigned an anonymous ID
3. **Create/Join game**: 
   - **Solo Practice**: Click "Play Local" for single-player against the engine
   - **Online Multiplayer**: Click "Play Online" to create a game room
4. **Share game**: Copy the URL and share with an opponent for multiplayer
5. **Play the game**:
   - **Black bans first** - the game starts with Black banning a White move
   - If it's your turn to **ban**, select an opponent's piece to disable their moves
   - If it's your turn to **move**, make a legal move on the board (cannot use banned moves)
   - Turn order: Black bans → White moves → White bans → Black moves → repeat
6. **Game features**: Real-time sync, sound effects, move history, and timer support

> See [RULES.md](./RULES.md) for detailed game rules, special move handling, and edge cases.

---

## 📡 WebSocket Protocol

### ban-chess.ts Type Definitions

The game uses the following TypeScript types from the ban-chess.ts library:

```ts
interface Move {
  from: string;
  to: string;
  promotion?: 'q' | 'r' | 'b' | 'n';
}

interface Ban {
  from: string;
  to: string;
}

type Action = 
  | { move: Move }
  | { ban: Ban };

interface ActionResult {
  success: boolean;
  action?: Action;
  san?: string;
  error?: string;
  newFen?: string;
  gameOver?: boolean;
  checkmate?: boolean;
  stalemate?: boolean;
}

interface HistoryEntry {
  turnNumber: number;
  player: 'white' | 'black';
  actionType: 'ban' | 'move';
  action: Ban | Move;
  san?: string;
  fen: string;
  bannedMove?: Ban;
}
```

### Client → Server messages

```ts
type ClientMsg =
  | { type: "create"; gameId: string }
  | { type: "join"; gameId: string }
  | { type: "ban"; gameId: string; ban: Ban }
  | { type: "move"; gameId: string; move: Move };
```

### Server → Client messages

```ts
type ServerMsg =
  | { 
      type: "state"; 
      fen: string; 
      pgn: string; 
      nextAction: "ban" | "move"; 
      legalMoves?: Move[]; 
      legalBans?: Ban[];
      history?: HistoryEntry[];
    }
  | { type: "error"; message: string; error?: string };
```

---

## 🖼️ Board UI

The board is rendered using [react-chessground](https://github.com/ruilisi/react-chessground):

* `fen`: current position
* `movable.dests`: built from `legalMoves` or `legalBans`
* `onMove`: sends `{ type: "move" }` or `{ type: "ban" }` to server

---

## 📜 License

* `ban-chess.ts` is MIT
* `react-chessground` is GPL-3.0
* This project is GPL-3.0 due to `react-chessground` dependency

---

## ✅ Current Status & Future Plans

### ✅ Completed Features
- ✅ **Anonymous-first gameplay**: Lichess-style instant play without authentication
- ✅ **Real-time multiplayer**: WebSocket-based gameplay
- ✅ **Sound system**: Game audio and sound controls
- ✅ **Comprehensive testing**: E2E test coverage with Playwright
- ✅ **User management**: Profile pages and username customization
- ✅ **Mobile support**: Responsive design for mobile devices
- ✅ **Game persistence**: Redis-backed state management

### 🚧 Future Roadmap
* [ ] **Database persistence**: Full game history storage (Postgres + Prisma)
* [ ] **Matchmaking system**: Automated player pairing and lobby
* [ ] **Spectator mode**: Watch games in progress
* [ ] **Analysis board**: Post-game analysis with ban history
* [ ] **Rating system**: ELO-based player ratings
* [ ] **Tournament mode**: Bracket-style competitions

## 📚 Documentation

For more detailed information, check out the comprehensive documentation:

- **[DEVELOPMENT.md](./docs/DEVELOPMENT.md)** - Development setup, debugging, and best practices
- **[ARCHITECTURE.md](./docs/ARCHITECTURE.md)** - Technical architecture and design decisions  
- **[DEPLOYMENT.md](./docs/DEPLOYMENT.md)** - Comprehensive production deployment guide
- **[OAUTH_SETUP.md](./docs/OAUTH_SETUP.md)** - OAuth provider configuration
- **[RULES.md](./RULES.md)** - Complete Ban Chess game rules

---

## 🧪 Testing

This project includes comprehensive end-to-end testing with Playwright:

```bash
# Run all tests
npm run test

# Run specific test suites
npm run test:auth         # Authentication flow tests
npm run test:game         # Game functionality tests  
npm run test:multiplayer  # Multiplayer game tests
npm run test:a11y         # Accessibility tests

# Interactive test runner
npm run test:ui
```

**Test Coverage:**
- Authentication flows (OAuth + Guest)
- Solo and multiplayer gameplay
- Ban/move mechanics and validation
- Real-time WebSocket synchronization
- Accessibility compliance
- Mobile responsiveness

---

### Credits

* **[ban-chess.ts](https://github.com/RNDev666/ban-chess.ts)** by [@RNDev666](https://github.com/RNDev666) - Core game engine
* **[@bezalel6/react-chessground](https://www.npmjs.com/package/@bezalel6/react-chessground)** - Enhanced React wrapper for Chessground
* **[Chessground](https://github.com/lichess-org/chessground)** by Lichess - Interactive chessboard library

---
