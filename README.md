# Timeline

Daily journaling app with streak tracking and visual timeline. Single-page application with client-side auth and Firebase Realtime Database persistence. Includes offline-first architecture with localStorage fallback.

## Overview

Timeline is a minimalist journaling system designed around daily capture and visual reflection. Core features:

- **Daily Entry**: One highlight per day, max 500 chars, multi-line support
- **Streak Tracking**: Consecutive day counter with visual badge
- **Visual Timeline**: Sidebar spine with scroll-fill progress, chronological feed
- **Session Management**: Client-side auth with localStorage cache and Firebase sync
- **Offline Support**: Full functionality without network; syncs on reconnect

## Architecture

### Frontend
- Single `index.html` with embedded CSS and vanilla JavaScript
- No build step, no dependencies beyond Firebase SDK
- LocalStorage for session state and data caching
- Firebase Realtime Database for user and entry data

### Data Model
```
users/
  {username}: {name, password, created}

data/
  {username}/
    {YYYY-MM-DD}: {raw, ts}
```

**Dates**: ISO 8601 format (e.g., `2025-07-07`)

### UI Components
- **Landing Screen**: Sign in / create account toggle, credentials input
- **Sidebar**: Logo, timeline spine with scroll-fill indicator, user profile, logout
- **Main Feed**: Today's entry card, "Your story so far" chronological timeline
- **Modal**: Full entry expansion with formatting preserved

## Installation

1. Create a Firebase project at [firebase.google.com](https://firebase.google.com)
2. Enable Realtime Database (test mode or configure rules)
3. Update `FIREBASE_DB_URL` in the script:
   ```javascript
   const FIREBASE_DB_URL = 'https://your-project.firebaseio.com';
   ```
4. Deploy `index.html` to any static host (GitHub Pages, Vercel, Netlify, etc.)

## Firebase Setup

Set security rules to allow public read/write (demo only):
```json
{
  "rules": {
    "users": {
      ".read": true,
      ".write": true
    },
    "data": {
      ".read": true,
      ".write": true
    }
  }
}
```

**Production**: Replace with proper auth via `firebase.google.com` and user-scoped security rules.

## Usage

### Sign In / Create Account
```
1. Enter name and password
2. Toggle between "sign in" and "new here?" modes
3. Click "enter →"
4. Session stored in localStorage (tl_sess_v1)
```

### Write Entry
```
1. Type in "Today" card textarea
2. Shift+Enter for newlines (plain Enter also works)
3. Click "archive it" to lock entry and add to timeline
4. Revisit later with "edit" button
```

### Browse Timeline
- **Sidebar spine**: Click any date to jump to feed item
- **Feed**: Chronological entries below "Your story so far"
- **Modal**: Click feed item to expand full text
- **Streak badge**: Top right, gold when 3+ consecutive days

### Sign Out
```
Click avatar + "sign out"
Clears session, returns to login
```

## Key Algorithms

### Streak Calculation
```javascript
// Start from today or yesterday (if no entry today)
// Walk backwards day-by-day until a gap is found
// Return count
```

### Spine Fill
```javascript
// Listen to scroll events
// Calculate: scrollY / (documentHeight - viewportHeight) * 100
// Update spine-fill height accordingly
```

### Newline Rendering
```javascript
// nl2html: Replace &, <, >, then \n → <br>
// Use innerHTML for feed/modal, textContent for sidebar (XSS prevention)
```

## Data Structures

### User Object
```javascript
{
  name: "Display Name",
  password: "plaintext",  // demo only!
  created: 1720308022000   // timestamp
}
```

### Entry Object
```javascript
{
  raw: "what was your highlight today?",
  ts: 1720308022000
}
```

## Development

```bash
python -m http.server 8000
# Open http://localhost:8000
```

## Performance

- Firebase latency: ~50–200ms typical
- LocalStorage lookup: <1ms
- DOM render: <100ms for 100+ entries
- Initial load: ~500ms (depends on Firebase response time)

## Security Considerations

**Current (demo only)**:
- Plain-text passwords stored in Firebase
- No encryption or access control
- Anyone with database URL can read/modify data

**For production**:
1. Replace password auth with Firebase Authentication (email/phone)
2. Enable user-scoped security rules
3. Encrypt sensitive data at rest
4. Use HTTPS (automatic on modern hosts)
5. Add rate limiting to prevent abuse
6. Consider audit logging for data changes

## Browser Support

- Chrome 90+
- Firefox 88+
- Safari 14+
- Mobile browsers (iOS Safari 14+, Chrome Mobile)

## Tech Stack

- HTML5 (semantic structure)
- CSS3 (Grid, Flexbox, CSS variables, backdrop-filter)
- Vanilla JavaScript (ES6+)
- Firebase Realtime Database
- LocalStorage API

## Future Considerations

- Dark mode toggle (CSS custom properties ready)
- Export to PDF/JSON
- Search by keyword or date range
- Tags for entry organization
- Image upload support
- Public read-only sharing
- Daily email reminders
- Mood tracking / word cloud analytics
