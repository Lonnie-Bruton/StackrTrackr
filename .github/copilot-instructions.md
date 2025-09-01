# StackrTrackr Copilot Instructions

## Project Overview
StackrTrackr is a **client-side precious metals inventory tracking application** that runs entirely in the browser with no backend dependencies. It supports Silver, Gold, Platinum, and Palladium tracking with spot price integration and premium calculations.

## Critical Architecture Patterns

### 1. Script Loading Order (MUST Follow)
```html
<!-- CRITICAL: file-protocol-fix.js MUST load first -->
<script src="./js/file-protocol-fix.js"></script>
<!-- Core modules in dependency order -->
<script defer src="js/debug-log.js"></script>
<script defer src="./js/constants.js"></script>
<script defer src="./js/state.js"></script>
<!-- init.js MUST load last -->
<script defer src="./js/init.js"></script>
```

### 2. localStorage Security Pattern
All localStorage keys MUST be in the `ALLOWED_STORAGE_KEYS` whitelist in `constants.js`:
```javascript
// BAD - will fail security check
localStorage.setItem('my-custom-key', data);

// GOOD - add to ALLOWED_STORAGE_KEYS first
const ALLOWED_STORAGE_KEYS = [..., 'my-custom-key'];
```

### 3. DOM Access Pattern
Always use `safeGetElement()` to prevent null reference errors:
```javascript
// BAD
const element = document.getElementById('myId');
element.addEventListener('click', handler); // Can throw if null

// GOOD
const element = safeGetElement('myId', true); // Required element
if (element) element.addEventListener('click', handler);
```

### 4. Data Persistence Pattern
Use the async/sync data layer for localStorage operations:
```javascript
// Async (preferred for new features)
await saveData(INVENTORY_KEY, inventory);
const data = await loadData(INVENTORY_KEY, []);

// Sync (legacy compatibility)
saveDataSync(INVENTORY_KEY, inventory);
const data = loadDataSync(INVENTORY_KEY, []);
```

## Key Application Components

### Core Data Flow
1. **`js/constants.js`** - Global config including API providers and storage keys
2. **`js/state.js`** - Application state (sorting, pagination, edit modes)
3. **`js/inventory.js`** - Core CRUD operations (2400+ lines, main business logic)
4. **`js/api.js`** - External pricing API integration with provider abstraction
5. **`js/utils.js`** - Data formatting, validation, and helper functions

### Premium Calculations
Non-collectable items use this formula:
```javascript
premiumPerOz = (price / weight) - spotPriceAtPurchase;
totalPremium = premiumPerOz * weight;
```

### API Provider Pattern
All external APIs use a unified interface in `constants.js`:
```javascript
const API_PROVIDERS = {
  PROVIDER_NAME: {
    name: "Display Name",
    baseUrl: "https://api.url",
    endpoints: { silver: "/endpoint", /* ... */ },
    parseResponse: (data) => data.rate?.price || null,
    batchSupported: true
  }
};
```

## File Protocol Compatibility
The app MUST work with `file://` protocol for local usage:
- `file-protocol-fix.js` provides localStorage fallbacks
- No server-side dependencies allowed
- All external resources via HTTPS CDNs
- CSP policy intentionally relaxed for local file execution

## Development Workflow

### Local Testing
```bash
# Open directly in browser
open index.html
# OR serve locally
python -m http.server 8000
```

### Code Quality Integration
- Uses Codacy MCP Server for quality analysis
- Always run `codacy_cli_analyze` after file edits
- Provider: `gh`, Organization: `Lonnie-Bruton`, Repository: `StackrTrackr.com`

### Data Import/Export
- CSV import via `PapaParse` library
- PDF export via `jsPDF` with AutoTable plugin
- Comprehensive ZIP backup functionality
- Supports multiple date formats with intelligent parsing

## Common Patterns

### Error Handling
```javascript
try {
  // Operation
} catch (error) {
  console.error('Operation failed:', error);
  debugLog('Detailed context:', { error, context });
}
```

### Theme Management
Four-state theme system: `light` → `dark` → `sepia` → `system` → (repeat)

### Performance Monitoring
Use `monitorPerformance()` wrapper for expensive operations:
```javascript
const result = monitorPerformance(expensiveFunction, 'operation-name', ...args);
```

## Project Context
- Related to HexTrackr (separate vulnerability tracking project)
- Recent focus on security and code quality improvements
- Client-side only architecture is intentional design decision
- Supports both cached and real-time spot price data
