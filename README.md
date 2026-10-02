# Context Budget Planner

Plan an agent's context window like a budget: allocate it across every consumer, watch the stacked bar, and find the overflow before production does.

**Live demo:** https://0xelitesystem.github.io/context-budget-planner/

## Live demo

https://0xelitesystem.github.io/context-budget-planner/

The sibling tools [prompt-cost-calculator](https://github.com/0xelitesystem/prompt-cost-calculator) and [prompt-token-meter](https://github.com/0xelitesystem/prompt-token-meter) price and measure a **single prompt**; this one **allocates a whole agent context window** across the consumers competing for it and shows you the overflow before production does.

## Features

- **Pick a window, or type your own.** A short list of model context limits as a convenience, plus generic window sizes. Every value is editable and the number you type always wins.
- **Named line items.** System prompt, tool definitions, conversation history, retrieved chunks (RAG), few-shot examples, scratchpad or memory, and a reserved output allowance. Add, rename, re-role, and remove them.
- **Three ways to size any item, which is the point.** Paste the actual text and have it estimated, enter a token count directly, or enter a unit count times a per-unit size (12 retrieved chunks at 400 tokens each; 9 tool definitions at 420 tokens each). The arithmetic is printed under every item.
- **A live stacked bar** of the whole window: each item's share, the reserved output block drawn as a distinct hatched segment, and the remaining headroom. When the total goes past the limit marker, the overflow is drawn in red, stated in tokens, and paired with a list of what to cut.
- **Checks a practitioner actually needs**, not decoration:
  - the output reserve is zero, or too small to hold the response you said you expect
  - retrieval alone consumes more than half the window
  - tool definitions have grown past a share threshold you set
  - history will overflow after N more turns at the current average turn size, with N computed and shown
  - the plan fits now, but leaves no room for a single additional tool result
- **Turns until overflow.** Given the current fixed cost and an average tokens-per-turn, how many more exchanges fit before the window is exhausted, with the division shown.
- **Cut guidance on overflow.** Exactly how many tokens over you are, which single item could absorb it on its own, and which items are too small to do it alone.
- **Export.** The whole plan as copyable Markdown for a design doc, or as JSON to check into a repo next to the prompt it describes.
- **Load sample** fills in a realistic support-agent budget (32k window, 35,379 tokens allocated, 3,379 over) so the overflow region, the cut list, and the tool-definition check all have something to say in one click. It does not trip every check, because several are mutually exclusive: a plan that is already over the limit cannot also be a plan that fits but has no room for one more tool result.
- Dark theme by default, light theme available, choice remembered. Works down to 360px wide. Keyboard accessible throughout. Single file, no external dependencies, no network requests, no analytics.

## How it works

Everything is arithmetic you can check by hand, which is why the arithmetic is printed.

**Token estimation is a heuristic, not a tokenizer.** Items sized by pasted text are counted as `characters / characters-per-token`, rounded up, with the divisor exposed as an editable input (default 4). This is an approximation. The true count varies by tokenizer and by language, and code, JSON, and non-Latin scripts tokenize very differently from English prose. Treat estimated items as approximate and enter measured counts when you have them.

- To see where real token boundaries fall, use [tokenizer-visualizer](https://github.com/0xelitesystem/tokenizer-visualizer).
- To turn a token count into money, use [prompt-cost-calculator](https://github.com/0xelitesystem/prompt-cost-calculator).

**Totals.** Consumers are summed, the reserved output block is summed separately, and the two are added. Headroom is `limit - total`. Overflow is `total - limit`. Share of window is `item tokens / limit`.

**Turns until overflow.** `floor((limit - total) / average tokens per turn)`. It assumes history is the only thing that grows and that nothing is compacted or truncated along the way, which is stated in the tool.

**Thresholds are yours, not a standard.** "Retrieval over half the window" and the tool-definition percentage are rules of thumb the tool exposes as inputs. No published standard sets them. Change them to match your workload.

**Model context limits are a convenience list, checked 2026-07-31, not an authority.** Limits change and vary by deployment. The list only contains entries the author is confident about; everything else is served by the generic window sizes. Every limit is editable and the entered value wins.

The core logic lives in pure functions (`computePlan`, `computeItemTokens`, `estimateTokensFromText`, `computeTurnsUntilOverflow`, `suggestCuts`, `planToMarkdown`, `planToJson`) that take input and return a result object with no DOM access, so they can be read, lifted, or tested on their own.

## Use

1. Pick a model context limit from the list, or type your own window size.
2. Add line items (system prompt, tool definitions, history, retrieved chunks and so on) and size each one by pasting its text, entering a token count, or entering a unit count times a per-unit size.
3. Watch the stacked bar and the checks: any overflow is drawn in red with a list of what to cut, plus the turns left before history fills the window.
4. Export the plan as Markdown or JSON. Press **Load sample** first if you want to see a plan that overflows.

## Why this exists

Agent prompts fail quietly when the system prompt, tool definitions, retrieval and history together outgrow the context window, and the usual discovery point is production. This tool lets you budget the window up front with arithmetic you can check by hand. It is a single HTML file with no tracking and no network calls, and it is MIT licensed.

## Privacy

Everything runs in your browser. Your prompts, your numbers, and your plan never leave the page. There are no network requests, no external dependencies, no fonts or scripts fetched from anywhere, and no analytics. Your plan and your theme choice are saved to `localStorage` on your own machine so the page survives a refresh; clearing site data removes them. Verify all of this by reading the single HTML file, or by opening DevTools and watching an empty network tab.

## Run locally

```
git clone https://github.com/0xelitesystem/context-budget-planner
cd context-budget-planner
```

Then open `index.html` in any modern browser, or serve the folder with `python -m http.server` and visit http://localhost:8000/.

## Build

No build step. The whole tool is one `index.html` file with inline CSS and JavaScript, and there is nothing to install or compile.

## License

MIT. See [LICENSE](LICENSE).

## More

- Every tool in the catalog: https://0xelitesystem.github.io/
- The shop behind it: https://elitesystem.ai
- [prompt-cost-calculator](https://github.com/0xelitesystem/prompt-cost-calculator), price a single prompt across providers
- [prompt-token-meter](https://github.com/0xelitesystem/prompt-token-meter), measure a single prompt as you type
- [tokenizer-visualizer](https://github.com/0xelitesystem/tokenizer-visualizer), see where token boundaries actually fall
