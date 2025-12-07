# Startup Games

> Gamification platform for founders, builders, and investors

Startup Games turns the entrepreneurial journey into an engaging experience with competitions, leaderboards, achievements, and quests—making every milestone worth celebrating.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      startup.games                           │
├─────────────────────────────────────────────────────────────┤
│  Connect → Track → Achieve → Compete → Celebrate            │
│                                                             │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐    │
│  │  Quests │   │Achieve- │   │ Leader- │   │Competi- │    │
│  │         │   │  ments  │   │ boards  │   │  tions  │    │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘    │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐    │
│  │ Streaks │   │XP/Levels│   │  Teams  │   │Celebrate│    │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘    │
├─────────────────────────────────────────────────────────────┤
│             api.startup.games  ←→  db.startup.games         │
└─────────────────────────────────────────────────────────────┘
```

## Installation

```bash
npm install startup.games
```

## Quick Start

```typescript
import { Games } from 'startup.games'

// Connect your startup
const player = await Games.connect({
  startup: 'MyStartup',
  player: 'founder@mystartup.com'
})

// Check your stats
console.log(player.level)         // 12
console.log(player.xp)            // 4,850
console.log(player.achievements)  // ['first-user', 'first-dollar', ...]

// Complete a quest
await player.quests.complete('first-customer')

// Check leaderboard rank
const rank = await player.leaderboard.rank('mrr')
console.log(rank)  // #42 of 1,234 startups
```

## Core Features

### Achievements

Unlock badges for hitting milestones:

```typescript
import { Achievements } from 'startup.games'

// View all available achievements
const all = await Achievements.list()

// View unlocked achievements
const unlocked = await player.achievements.unlocked()

// Check specific achievement
const firstUser = await player.achievements.get('first-user')
// {
//   id: 'first-user',
//   name: 'First User',
//   description: 'Get your first user',
//   unlockedAt: '2024-01-15T10:30:00Z',
//   xp: 100
// }
```

### Achievement Categories

| Category | Examples |
|----------|----------|
| **Launch** | First Deploy, First Domain, First User |
| **Growth** | 100 Users, 1K Users, 10K Users |
| **Revenue** | First Dollar, $1K MRR, $10K MRR |
| **Product** | First Feature, API Launch, Mobile App |
| **Community** | First Review, Featured on PH |

### Quests

Structured challenges that guide your journey:

```typescript
import { Quests } from 'startup.games'

// View available quests
const available = await player.quests.available()

// Start a quest
await player.quests.start('launch-week')

// Track progress
const progress = await player.quests.progress('launch-week')
// {
//   id: 'launch-week',
//   name: 'Launch Week',
//   steps: [
//     { id: 'prepare-launch', completed: true },
//     { id: 'producthunt', completed: false },
//     { id: 'social-announce', completed: false }
//   ],
//   progress: 33,
//   reward: { xp: 500, badge: 'launcher' }
// }

// Complete quest
await player.quests.complete('launch-week')
```

### Leaderboards

Rankings across key metrics:

```typescript
import { Leaderboards } from 'startup.games'

// View leaderboard
const mrr = await Leaderboards.get('mrr')

// Get your rank
const rank = await player.leaderboard.rank('mrr')

// Available leaderboards
const boards = await Leaderboards.list()
// ['mrr', 'users', 'growth-rate', 'xp', 'achievements', 'streak']
```

### Competitions

Time-bound challenges with prizes:

```typescript
import { Competitions } from 'startup.games'

// View active competitions
const active = await Competitions.active()

// Join a competition
await player.competitions.join('launch-challenge-2024')

// Check standings
const standings = await Competitions.standings('launch-challenge-2024')

// Submit entry
await player.competitions.submit('launch-challenge-2024', {
  url: 'https://mystartup.build',
  description: 'My launch submission'
})
```

### Streaks

Track consistent execution:

```typescript
// View your streaks
const streaks = await player.streaks.list()

// {
//   daily: { current: 15, best: 42 },
//   weekly: { current: 8, best: 12 },
//   shipping: { current: 5, best: 10 }
// }

// Streak types
// - Daily login/activity
// - Weekly shipping (at least one deploy)
// - Content streak (blog/social posts)
// - Sales streak (new customers)
```

### XP & Levels

```typescript
// View XP breakdown
const xp = await player.xp.breakdown()

// {
//   total: 4850,
//   level: 12,
//   nextLevel: 5000,
//   progress: 97,
//   sources: {
//     achievements: 2500,
//     quests: 1500,
//     competitions: 500,
//     streaks: 350
//   }
// }

// Level thresholds
// Level 1: 0 XP
// Level 5: 500 XP
// Level 10: 2,500 XP
// Level 20: 10,000 XP
// Level 50: 100,000 XP
```

### Teams

Compete as a team:

```typescript
import { Teams } from 'startup.games'

// Create a team
const team = await Teams.create({
  name: 'Builder Squad',
  members: ['alice@example.com', 'bob@example.com']
})

// Team leaderboards
const teamRank = await team.leaderboard.rank('combined-mrr')

// Team competitions
await team.competitions.join('studio-showdown-2024')
```

### Celebrations

Share and celebrate wins:

```typescript
// Celebrate a milestone
await player.celebrate({
  type: 'milestone',
  message: 'Just hit $10K MRR! 🎉',
  metric: 'mrr',
  value: 10000
})

// View community celebrations
const feed = await Games.celebrations.feed()
```

## Integration with Builder Tools

### startup-builder

```typescript
import { Startup } from 'startup-builder'
import { Games } from 'startup.games'

// Progress auto-tracked
const startup = await Startup.create({
  name: 'MyStartup',
  gamification: true  // Enable auto-tracking
})

// Achievements unlock automatically:
// - Building completes → "First Deploy" achievement
// - Site launches → "Live Site" achievement
// - First user signs up → "First User" achievement
```

### sales-builder

```typescript
import { Sales } from 'sales-builder'
import { Games } from 'startup.games'

// Sales milestones auto-tracked
const sales = await Sales.create({
  product: 'MyStartup',
  gamification: true
})

// Achievements unlock automatically:
// - First lead → "Lead Magnet" achievement
// - First customer → "First Dollar" achievement
// - 10 customers → "Growing Business" achievement
```

## Configuration

```typescript
// games.config.ts
import { defineConfig } from 'startup.games'

export default defineConfig({
  startup: 'MyStartup',
  notifications: {
    achievements: true,
    leaderboard: true,
    competitions: true
  },
  privacy: {
    publicProfile: true,
    showRevenue: false,
    showUsers: true
  }
})
```

## Related Packages

| Package | Description |
|---------|-------------|
| [startup-builder](https://github.com/StartupsStudio/startup-builder) | Build autonomous startups |
| [sales-builder](https://github.com/StartupsStudio/sales-builder) | Autonomous sales workflows |
| [builder.domains](https://github.com/StartupsStudio/builder.domains) | Free domains for builders |

## License

MIT
