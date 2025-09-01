# StackrTrackr Agent Information

## Project Identity
- **Repository**: StackrTrackr.com
- **Owner**: Lonnie-Bruton  
- **Project Type**: Client-side precious metals inventory tracking application
- **Primary Languages**: JavaScript, HTML, CSS
- **Architecture**: Pure client-side with localStorage persistence

## Key Distinguishing Features
- **No backend required** - works with `file://` protocol
- **localStorage-first** data persistence
- **Multiple metals support**: Silver, Gold, Platinum, Palladium
- **Premium calculations** for non-collectable items
- **Multi-format import/export** (CSV, JSON, PDF)
- **External API integration** for spot price tracking

## Critical File Dependencies
```
js/file-protocol-fix.js  # MUST load first for file:// compatibility
js/constants.js          # Global config and API providers
js/state.js             # Application state management
js/inventory.js         # Core CRUD operations (2400+ lines)
js/api.js               # External pricing API integration
```

## Development Notes
- All localStorage keys must be in `ALLOWED_STORAGE_KEYS` whitelist
- Use `safeGetElement()` pattern for DOM access to prevent null refs
- API integration supports multiple providers with unified interface
- Premium calculations: `(price/weight) - spotPriceAtPurchase`

## Code Quality Status
- **Current Focus**: Codacy code quality analysis and issue resolution
- **Memory Context**: Previous successful security sprint completion for HexTrackr
- **Next Priority**: Address ESLint configuration and code quality issues

## Related Projects
- **HexTrackr**: Separate project - comprehensive vulnerability tracking system
- **Confusion Note**: Agents should verify project context via git remote or repo path

This document helps agents distinguish between multiple projects and understand StackrTrackr-specific requirements.
