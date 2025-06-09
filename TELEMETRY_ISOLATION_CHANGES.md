# Telemetry Isolation Changes

## Summary
Modified `src/telemetry.ts` to completely prevent ALL network calls when `PROMPTFOO_DISABLE_TELEMETRY=1` is set, ensuring complete data isolation for self-hosted deployments.

## Changes Made

### 1. Modified `send()` method in `src/telemetry.ts`
**Before:** When telemetry was disabled, it would still send a "telemetry disabled" event to `https://api.promptfoo.dev/telemetry`

**After:** When telemetry is disabled, the method returns early without making any network calls:
```typescript
async send(): Promise<void> {
  // Early return if telemetry is disabled - prevent all network calls
  if (this.disabled) {
    this.events = []; // Clear events without sending
    return;
  }
  // ... rest of method only executes when telemetry is enabled
}
```

### 2. Modified `saveConsent()` method in `src/telemetry.ts`
**Before:** Would always attempt to save consent to `https://api.promptfoo.dev/consent`

**After:** When telemetry is disabled, the method returns early without making any network calls:
```typescript
async saveConsent(email: string, metadata?: Record<string, string>): Promise<void> {
  // Early return if telemetry is disabled - prevent all network calls
  if (this.disabled) {
    logger.debug('Telemetry disabled - skipping consent save');
    return;
  }
  // ... rest of method only executes when telemetry is enabled
}
```

### 3. Updated tests in `test/telemetry.test.ts`
- Modified tests to expect NO network calls when telemetry is disabled
- Added test for `saveConsent()` isolation
- Updated test descriptions to reflect new behavior

## Result
When `PROMPTFOO_DISABLE_TELEMETRY=1` is set:
- ✅ No data is sent to `https://api.promptfoo.dev/telemetry`
- ✅ No data is sent to `https://api.promptfoo.dev/consent`
- ✅ No "telemetry disabled" events are transmitted
- ✅ Complete network isolation from promptfoo servers
- ✅ All functionality remains intact for local evaluation

## Complete Self-Hosted Isolation Configuration

```bash
# Environment variables for complete isolation
PROMPTFOO_DISABLE_TELEMETRY=1
PROMPTFOO_DISABLE_UPDATE=1
PROMPTFOO_DISABLE_REDTEAM_REMOTE_GENERATION=1
PROMPTFOO_DISABLE_SHARING=1
PROMPTFOO_SELF_HOSTED=1
```

## Docker Configuration
```yaml
version: '3.8'
services:
  promptfoo:
    image: ghcr.io/promptfoo/promptfoo:latest
    ports:
      - '3000:3000'
    volumes:
      - ./promptfoo_data:/home/promptfoo/.promptfoo
    environment:
      - PROMPTFOO_DISABLE_TELEMETRY=1
      - PROMPTFOO_DISABLE_UPDATE=1
      - PROMPTFOO_DISABLE_REDTEAM_REMOTE_GENERATION=1
      - PROMPTFOO_DISABLE_SHARING=1
      - PROMPTFOO_SELF_HOSTED=1
      - OPENAI_API_KEY=your_key_here
```

This ensures your self-hosted promptfoo instance will not send ANY data to external promptfoo servers.
