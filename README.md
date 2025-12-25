# SillyTavern Context Truncator

Batch-based context truncation to prevent cache invalidation when using LLMs with caching support.

## Purpose

When using LLMs with caching (like Claude), removing messages one-by-one invalidates the cache on every generation, causing slower responses and higher costs. This extension removes messages in fixed batches, minimizing cache invalidation while keeping context under control.

**Note:** The target context size is approximate and may vary by ±10% due to batch-based truncation. This is intentional to preserve cache efficiency.

## How It Works

1. **Monitors** the previous prompt size using SillyTavern's raw context API
2. **Detects** when the context exceeds your target size
3. **Removes** N messages at once (batch truncation)
4. **Continues** removing batches until context is back under target
5. **Preserves** a minimum number of recent messages for safety

## Installation

1. Open SillyTavern
2. Go to **Extensions** > **Install Extension**
3. Enter: `https://github.com/Shadota/SillyTavern-Context-Truncator`
4. Click **Install**

## Configuration

### Settings

- **Enable Batch Truncation**: Toggle the extension on/off
- **Target Context Size (±10%)**: The approximate size (in tokens) to trim down to (default: 8000). Actual size may vary by ±10% due to batch truncation.
- **Batch Size**: Number of messages to remove per batch (default: 20)
- **Min Messages to Keep**: Safety limit - never go below this many messages (default: 10)
- **Debug Mode**: Enable detailed logging to browser console

### Status Display

The status panel shows real-time information:
- **Current Context**: Size of the previous prompt in tokens (color-coded: 🔴 Red if >110% of target, 🟡 Yellow if within ±10%, 🟢 Green if <90%)
- **Target**: Your configured target size (±10% variance expected)
- **Batch Size**: Current batch size setting
- **Truncation Index**: Where truncation starts (first message to keep)
- **Total Messages**: Total messages in chat
- **Kept Messages**: Messages currently kept in context

### Controls

- **Reset Truncation**: Resets the truncation index (useful after deleting messages)
- **Refresh Status**: Manually updates the status display

## Example Scenario

**Setup:**
- Target context: 8000 tokens (±10% = 7200-8800 acceptable range)
- Batch size: 20 messages
- Min keep: 10 messages
- Chat has 100 messages

**Execution:**
1. **Generation 1**: Context = 7500 tokens → No truncation (within range)
2. **Generation 2**: Context = 8900 tokens → Remove messages 0-19 (batch 1)
3. **Generation 3**: Context = 8200 tokens → No more truncation (within range)
4. **Generation 4**: Context = 7800 tokens → No truncation (within range)
5. **Generation 5+**: Context stays within 7200-8800 → **Cache preserved!**

**Note:** The actual context size will typically vary within ±10% of your target due to batch-based truncation. This is expected behavior and ensures cache efficiency.

## Benefits

- **Cache Efficiency**: 20x fewer cache invalidations (removing 20 at once vs 1 at a time)
- **Predictable Behavior**: Always removes fixed batches, not variable amounts
- **Approximate Targeting**: Maintains context within ±10% of target for optimal cache preservation
- **Safety**: Minimum message setting prevents over-truncation
- **Transparency**: Color-coded status display shows exactly what's happening
- **Automatic**: Works seamlessly in the background

## Requirements

- SillyTavern v1.14.0 or higher

## Troubleshooting

### Extension not loading
- Make sure you're on SillyTavern v1.14.0 or higher
- Check browser console (F12) for errors
- Try reloading the page

### Truncation not working
- Enable **Debug Mode** in settings
- Check browser console for debug logs
- Verify **Enable Batch Truncation** is checked
- Check that **Target Context Size** is set appropriately

### Status display shows "-"
- Wait for a message to be generated first
- Click the **Refresh Status** button
- Check that the extension is enabled

## License

MIT License - See LICENSE file for details

## Author

Shadota

## Contributing

Issues and pull requests welcome at: https://github.com/Shadota/SillyTavern-Context-Truncator