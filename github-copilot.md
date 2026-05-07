# GitHub Copilot

> *Click &#9733; if you like the project. Your contributions are heartily ♡ welcome.*

<br/>

## Table of Contents

* [Getting Started](#-1-getting-started)
* [Code Completions](#-2-code-completions)
* [Copilot Chat](#-3-copilot-chat)
* [Slash Commands](#-4-slash-commands)
* [Chat Variables and Participants](#-5-chat-variables-and-participants)
* [Prompt Engineering](#-6-prompt-engineering)
* [Copilot Edits and Agent Mode](#-7-copilot-edits-and-agent-mode)
* [Security](#-8-security)
* [Extensions](#-9-extensions)
* [Code Reviews and Pull Requests](#-10-code-reviews-and-pull-requests)
* [Copilot Workspace](#-11-copilot-workspace)
* [Context and Codebase](#-12-context-and-codebase)
* [Custom Instructions](#-13-custom-instructions)
* [Unit Test Generation](#-14-unit-test-generation)
* [Organization Administration](#-15-organization-administration)
* [Privacy and Data](#-16-privacy-and-data)
* [Language Support](#-17-language-support)
* [Enterprise Best Practices](#-18-enterprise-best-practices)

<br/>

## # 1. GETTING STARTED

<br/>

## Q. What is GitHub Copilot and how does it work?

**GitHub Copilot** is an AI-powered coding assistant developed by GitHub and OpenAI. It uses large language models (LLMs) — currently based on OpenAI\'s GPT-4o and specialized code models — to suggest code completions, generate functions, write tests, explain code, and assist with debugging directly inside your editor.

**How it works:**
1. Copilot reads the **context window** — open files, cursor position, comments, and related files.
2. It sends a prompt (your context) to the model hosted on GitHub\'s infrastructure.
3. The model returns one or more code suggestions.
4. You accept (Tab), cycle through alternatives (Alt+] / Alt+[), or dismiss (Esc).

**Key capabilities (2025/2026):**
- Inline code completions
- Multi-line ghost text suggestions
- Copilot Chat (conversational AI in the editor)
- Copilot Edits / Agent mode (multi-file edits)
- PR summaries and code reviews
- CLI integration (`gh copilot`)

```bash
# Install GitHub CLI and GitHub Copilot CLI extension
winget install GitHub.cli
gh auth login
gh extension install github/gh-copilot

# Ask a CLI command using Copilot
gh copilot suggest "list all running docker containers with their ports"
gh copilot explain "git rebase -i HEAD~3"
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 2. CODE COMPLETIONS

<br/>

## Q. What are the different GitHub Copilot plans and features?

GitHub Copilot is offered in four tiers as of 2025/2026:

| Plan | Target | Key Features |
|------|--------|-------------|
| **Copilot Free** | Individual developers | 2,000 completions/month, 50 chat msgs/month, limited models |
| **Copilot Pro** | Individual ($10/mo) | Unlimited completions + chat, all models (GPT-4o, Claude, Gemini), Copilot Edits |
| **Copilot Business** | Teams ($19/user/mo) | Everything in Pro + org policy controls, audit logs, IP indemnity |
| **Copilot Enterprise** | Large orgs ($39/user/mo) | Everything in Business + Copilot Workspace, PR summaries, codebase indexing, fine-tuning |

**Model selection (Pro/Business/Enterprise):**

| Model | Strengths |
|-------|-----------|
| GPT-4o | Fast, general-purpose |
| Claude Sonnet 3.7 | Complex reasoning, long context |
| Gemini 1.5 Pro | Google ecosystem integration |
| o3-mini | Advanced math/reasoning |

```json
// .vscode/settings.json — select default Copilot model
{
  "github.copilot.chat.defaultModel": "gpt-4o",
  "github.copilot.enable": {
    "*": true,
    "plaintext": false,
    "markdown": true
  }
}
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## Q. How do you install and configure GitHub Copilot in VS Code?

**Installation steps:**

1. Open VS Code → Extensions (`Ctrl+Shift+X`)
2. Search **"GitHub Copilot"** → install the **GitHub Copilot** extension
3. Also install **GitHub Copilot Chat** (usually installed as a dependency)
4. Sign in with your GitHub account when prompted
5. Verify: the Copilot icon appears in the status bar (bottom right)

**Configuration options in `settings.json`:**

```json
{
  // Enable/disable per language
  "github.copilot.enable": {
    "*": true,
    "plaintext": false,
    "markdown": true,
    "yaml": true
  },

  // Inline suggestions
  "editor.inlineSuggest.enabled": true,
  "github.copilot.inlineSuggest.enable": true,

  // Chat settings
  "github.copilot.chat.localeOverride": "en",
  "github.copilot.chat.useProjectTemplates": true,

  // Agent / Edits mode
  "github.copilot.chat.agent.thinkingTool": true
}
```

**Key keyboard shortcuts (VS Code):**

| Action | Windows/Linux | macOS |
|--------|--------------|-------|
| Accept suggestion | `Tab` | `Tab` |
| Dismiss suggestion | `Esc` | `Esc` |
| Next suggestion | `Alt+]` | `Option+]` |
| Previous suggestion | `Alt+[` | `Option+[` |
| Open Copilot Chat | `Ctrl+Alt+I` | `Ctrl+Cmd+I` |
| Inline Chat | `Ctrl+I` | `Cmd+I` |
| Open Copilot Edits | `Ctrl+Shift+I` | `Cmd+Shift+I` |

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 3. COPILOT CHAT

<br/>

## Q. How does GitHub Copilot generate inline code suggestions?

Copilot generates ghost-text suggestions as you type. The context it uses includes:

- **Current file** — code before and after the cursor
- **Open tabs** — other files open in your editor (neighboring tabs heuristic)
- **Language and framework** — detected from file extension and imports
- **Comments and docstrings** — natural language intent
- **Project structure** — file names and paths visible in the workspace

**Example: Function completion from a comment**

```python
# Calculate the compound interest given principal, rate, time and n (compoundings per year)
def compound_interest(principal: float, rate: float, time: float, n: int = 12) -> float:
    # Copilot suggests:
    return principal * (1 + rate / n) ** (n * time)
```

**Example: Complete a REST API handler from function signature**

```typescript
// GET /users/:id — return user by ID from database, 404 if not found
async function getUserById(req: Request, res: Response): Promise<void> {
  // Copilot fills in:
  const { id } = req.params;
  try {
    const user = await db.users.findUnique({ where: { id: parseInt(id) } });
    if (!user) {
      res.status(404).json({ error: 'User not found' });
      return;
    }
    res.json(user);
  } catch (error) {
    res.status(500).json({ error: 'Internal server error' });
  }
}
```

**Tips to improve suggestions:**
- Write descriptive function names and parameter names
- Add a comment describing intent before the cursor
- Keep related files open in tabs (Copilot uses neighboring tabs as context)
- Use docstring-style comments for complex logic

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 4. SLASH COMMANDS

<br/>

## Q. What is GitHub Copilot Chat and how does it differ from inline completions?

**GitHub Copilot Chat** is a conversational AI interface embedded in the editor. Unlike inline completions (which auto-suggest as you type), Chat requires you to explicitly ask questions and receives full multi-paragraph responses with code blocks.

| Feature | Inline Completions | Copilot Chat |
|---------|--------------------|-------------|
| Trigger | Automatic (as you type) | Manual (you ask) |
| Output | Code only (ghost text) | Text + code blocks |
| Use case | Completing code in context | Explain, refactor, debug, ask questions |
| Scope | Current file/cursor | Can reference whole workspace |
| Mode | Passive | Interactive |

**Chat panels available in VS Code:**

- **Chat view** (sidebar) — persistent conversation panel
- **Inline Chat** (`Ctrl+I`) — ask a question directly in the editor
- **Quick Chat** (`Ctrl+Shift+Alt+L`) — floating chat window

**Example interactions:**

```
# In Copilot Chat:
User: What does this function do?
[select code] → right-click → Copilot → Explain

User: /fix the bug in the selected code

User: /tests generate unit tests for the UserService class

User: #file:auth.ts How is JWT validation handled in this file?
```

```typescript
// Inline Chat example — select a function, press Ctrl+I, type:
// "Refactor this to use async/await instead of callbacks"
// Copilot rewrites the function in-place
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 5. CHAT VARIABLES AND PARTICIPANTS

<br/>

## Q. What are slash commands in GitHub Copilot Chat?

**Slash commands** are shortcuts in Copilot Chat that trigger specific actions on selected code or files, so you don\'t have to write detailed prompts every time.

| Command | Action |
|---------|--------|
| `/explain` | Explain how the selected code works |
| `/fix` | Suggest a fix for problems in selected code |
| `/tests` | Generate unit tests for selected code |
| `/doc` | Add documentation comments to selected code |
| `/simplify` | Simplify selected code |
| `/new` | Create a new project/file from a description |
| `/newNotebook` | Create a new Jupyter notebook |
| `/clear` | Clear the chat history |
| `/help` | Show available commands |

**Examples:**

```
# Explain a complex regex
/explain (?<![a-zA-Z0-9._%+-])([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})

# Fix a bug in selected code
/fix  ← (with buggy code selected)

# Generate tests
/tests generate tests for the PaymentService class using Jest

# Document a class
/doc  ← (with a class selected)
```

```python
# Before /doc:
def retry(func, max_attempts=3, delay=1.0):
    for attempt in range(max_attempts):
        try:
            return func()
        except Exception as e:
            if attempt == max_attempts - 1:
                raise
            time.sleep(delay)

# After /doc (Copilot generates):
def retry(func, max_attempts=3, delay=1.0):
    """
    Retry a function call up to max_attempts times with a delay between retries.

    Args:
        func: Callable to execute.
        max_attempts: Maximum number of retry attempts (default: 3).
        delay: Seconds to wait between retries (default: 1.0).

    Returns:
        The return value of func on success.

    Raises:
        Exception: Re-raises the last exception if all retries fail.
    """
    for attempt in range(max_attempts):
        try:
            return func()
        except Exception as e:
            if attempt == max_attempts - 1:
                raise
            time.sleep(delay)
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 6. PROMPT ENGINEERING

<br/>

## Q. What are chat variables and participants in Copilot Chat?

**Chat variables** (prefixed with `#`) attach specific context to your prompt — files, symbols, selections, or the terminal output — without copy-pasting content manually.

| Variable | Description |
|----------|-------------|
| `#file` | Include a specific file as context |
| `#selection` | Include currently selected code |
| `#editor` | Include the active editor\'s content |
| `#codebase` | Search the entire indexed codebase |
| `#terminalLastCommand` | Include last terminal command + output |
| `#terminalSelection` | Include selected text from the terminal |

**Chat participants** (prefixed with `@`) route your question to a specialized agent:

| Participant | Purpose |
|------------|---------|
| `@workspace` | Ask questions about the entire codebase |
| `@vscode` | Questions about VS Code settings and extensions |
| `@terminal` | Help with terminal commands |
| `@github` | Search GitHub issues, PRs, and docs |

**Examples:**

```
# Ask about a specific file
#file:src/auth/jwt.service.ts Explain how token refresh is implemented

# Use codebase search
@workspace Where is the database connection string configured?

# Reference last terminal error
#terminalLastCommand What caused this error and how do I fix it?

# VS Code settings help
@vscode How do I configure multi-root workspaces?

# GitHub search
@github What\'s new in the latest release of this repository?
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 7. COPILOT EDITS AND AGENT MODE

<br/>

## Q. How do you write effective prompts for GitHub Copilot?

Good prompts give Copilot enough context to generate accurate, relevant code. Follow these principles:

**1. Be specific and explicit**
```python
# Vague prompt (poor):
# function to process data

# Better prompt:
# Parse a CSV file at the given path and return a list of dicts.
# Skip rows where the 'status' column is 'inactive'.
# Handle FileNotFoundError by raising a ValueError with a descriptive message.
def parse_active_users(csv_path: str) -> list[dict]:
```

**2. Provide input/output examples**
```javascript
// Convert a snake_case string to camelCase.
// Examples: "user_first_name" → "userFirstName", "api_key" → "apiKey"
function toCamelCase(str) {
```

**3. Reference existing patterns in comments**
```typescript
// Similar to the existing UserRepository.findById() pattern in src/repositories/user.repo.ts,
// implement findByEmail that queries by email and throws NotFoundException if not found
async findByEmail(email: string): Promise<User> {
```

**4. Use docstrings to drive implementation**
```csharp
/// <summary>
/// Validates a credit card number using the Luhn algorithm.
/// Returns true if valid, false otherwise.
/// Throws ArgumentException if the input is null or empty.
/// </summary>
/// <param name="cardNumber">Card number string, digits only.</param>
public static bool ValidateCreditCard(string cardNumber)
{
    // Copilot fills in the full Luhn implementation
}
```

**5. Iterate with inline chat**
```
Select generated code → Ctrl+I → "Make this thread-safe using a lock"
Select generated code → Ctrl+I → "Add input validation and return error messages"
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 8. SECURITY

<br/>

## Q. What is GitHub Copilot Edits (agent mode)?

**GitHub Copilot Edits** (also called **Agent mode** in VS Code) allows Copilot to autonomously make changes across **multiple files** in your workspace to complete a larger task. Unlike inline completions or Chat (single file), Edits plans and executes multi-step code changes.

**How it works:**
1. Open the Copilot Edits panel (`Ctrl+Shift+I`)
2. Add working set files (the files Copilot can edit)
3. Describe the task in natural language
4. Copilot creates an edit plan, makes changes, and shows a diff
5. Review and Accept/Discard each change

**Example tasks for Copilot Edits:**

```
"Add input validation to all API route handlers in the routes/ folder"

"Refactor the UserService class to use dependency injection and update all callers"

"Add OpenTelemetry tracing to all service methods in src/services/"

"Migrate all callback-based async functions in utils/ to async/await"
```

**Agent mode with tools (autonomous):**

In Agent mode, Copilot can also run terminal commands, read file outputs, and iterate — like a junior developer executing your instructions:

```
"Create a new Express.js REST API with CRUD endpoints for a Product entity,
 set up Jest testing, add a Dockerfile, and update the README."
```

Copilot will:
1. Create `src/routes/product.route.ts`
2. Create `src/controllers/product.controller.ts`
3. Create `src/models/product.model.ts`
4. Create `__tests__/product.test.ts`
5. Create `Dockerfile`
6. Update `README.md`

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 9. EXTENSIONS

<br/>

## Q. How does GitHub Copilot handle security vulnerabilities in suggestions?

GitHub Copilot includes a **Vulnerability Prevention Filter** that blocks suggestions containing known security vulnerabilities such as SQL injection, hardcoded credentials, path traversal, and insecure random number usage.

**What Copilot blocks automatically:**

| Vulnerability | Example blocked pattern |
|---------------|------------------------|
| SQL injection | String concatenation in SQL queries |
| Hardcoded secrets | API keys, passwords, tokens in code |
| Path traversal | `../` in user-controlled file paths |
| Insecure crypto | `Math.random()` for security tokens |
| XSS | Unescaped user input in HTML |

**Safe vs blocked examples:**

```python
# BLOCKED — SQL injection risk:
query = f"SELECT * FROM users WHERE username = '{username}'"

# Copilot suggests instead (parameterized):
query = "SELECT * FROM users WHERE username = ?"
cursor.execute(query, (username,))
```

```javascript
// BLOCKED — hardcoded secret:
const apiKey = "sk-prod-abc123xyz789secret";

// Copilot suggests instead:
const apiKey = process.env.API_KEY;
if (!apiKey) throw new Error("API_KEY environment variable is required");
```

**Additional security features:**
- **Duplication detection** — filters suggestions that closely match public repository code with restrictive licenses
- **Copilot Autofix** — automatically suggests fixes for security alerts in GitHub Advanced Security (GHAS)
- **Secret scanning integration** — prevents committing detected secrets

```bash
# Enable Copilot Autofix for a repository (requires GHAS)
gh api repos/:owner/:repo/code-security/configurations \
  --method PATCH \
  --field copilot_autofix_security_alerts=true
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 10. CODE REVIEWS AND PULL REQUESTS

<br/>

## Q. What are GitHub Copilot Extensions and how do you use them?

**GitHub Copilot Extensions** allow third-party tools and services to integrate directly into Copilot Chat as **participants** (using the `@` prefix). They let you query external services, databases, and cloud providers without leaving your editor.

**Installing an extension:**
1. Go to [github.com/marketplace](https://github.com/marketplace) → filter by **Copilot Extensions**
2. Click the extension → Install it to your account or organization
3. In VS Code Copilot Chat, use `@extension-name` to invoke it

**Popular extensions (2025/2026):**

| Extension | Command | Use case |
|-----------|---------|----------|
| Docker | `@docker` | Generate Dockerfiles, explain compose configs |
| Azure | `@azure` | Deploy resources, query Azure services |
| Sentry | `@sentry` | Ask about production errors and traces |
| Datastax | `@datastax` | Query Cassandra/Astra DB schemas |
| LaunchDarkly | `@launchdarkly` | Manage feature flags |

**Example usage:**

```
# Docker extension
@docker Generate a multi-stage Dockerfile for a Node.js 22 app with a non-root user

# Azure extension
@azure What is the current status of my App Service "api-prod" in East US?

@azure Create a Bicep template for an Azure Function App with a Storage trigger

# Sentry extension
@sentry What are the top 5 errors in production this week?
```

**Building a custom extension:**

```javascript
// A Copilot Extension is a GitHub App with a chat handler
// It receives Copilot messages and returns SSE responses

app.post('/api/chat', async (req, res) => {
  const { messages } = req.body;
  const lastMessage = messages[messages.length - 1].content;

  // Query your internal service
  const result = await myInternalService.query(lastMessage);

  // Return as Copilot-compatible SSE stream
  res.setHeader('Content-Type', 'text/event-stream');
  res.write(`data: ${JSON.stringify({ content: result })}\n\n`);
  res.end();
});
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 11. COPILOT WORKSPACE

<br/>

## Q. How do you use Copilot for code reviews and pull requests?

GitHub Copilot integrates into the pull request workflow in several ways:

**1. Copilot PR Summary (Enterprise)**

On GitHub.com, Copilot can auto-generate a PR description summarizing what changed and why:
- Go to a PR → Click **Copilot** icon → **Generate summary**
- Copilot reads all diffs and produces: summary, type of change, files affected, and testing done

**2. Copilot Code Review**

```
In a PR on GitHub.com:
1. Go to the PR → "Files changed" tab
2. Click "Copilot review" (or the Copilot icon in the review panel)
3. Copilot posts inline comments on potential issues
```

**3. Copilot Autofix (GHAS)**

When CodeQL or secret scanning finds a vulnerability, Copilot Autofix automatically suggests a code fix inline in the PR:

```yaml
# Copilot Autofix appears as a suggested commit in the PR
# Example: CodeQL detects SQL injection in line 42
# Copilot suggests parameterized query as a code fix
```

**4. Review in VS Code with Chat**

```
# In VS Code, open the PR diff view, select suspicious code, then:
Ctrl+I → "Is there a security issue with this code?"
Ctrl+I → "Does this correctly handle null/undefined inputs?"

# Or in Chat:
#selection Review this code for correctness and edge cases
```

**5. `gh` CLI + Copilot**

```bash
# Summarize a PR from the terminal
gh pr view 123 | gh copilot explain

# Check PR diff for issues
gh pr diff 123 | gh copilot explain "Are there any bugs in this diff?"
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 12. CONTEXT AND CODEBASE

<br/>

## Q. What is GitHub Copilot Workspace?

**GitHub Copilot Workspace** is a cloud-based, AI-native development environment (currently in preview for Enterprise) where Copilot plans, implements, tests, and iterates on changes to complete GitHub **Issues** end-to-end.

**Workflow:**
1. Open any GitHub Issue
2. Click **"Open in Copilot Workspace"**
3. Copilot analyzes the issue, reads the codebase, and creates a **plan** (a list of files and changes needed)
4. You review and edit the plan
5. Copilot implements the plan (writes the code)
6. You can run tests, iterate, and create a PR — all in the browser

**Key features:**
- Fully browser-based (no local setup)
- Reads and writes to the repository
- Can run terminal commands and tests
- Integrates with GitHub Actions for CI
- One-click "Create PR" from the workspace

**Example scenario:**

```
Issue: "Add rate limiting to the /api/auth/login endpoint — max 5 requests per IP per minute"

Copilot Workspace plan:
1. Install express-rate-limit package
2. Create src/middleware/rateLimiter.ts with config
3. Apply middleware in src/routes/auth.route.ts
4. Add unit tests in __tests__/rateLimiter.test.ts
5. Update README.md with rate limiting documentation

[Copilot implements all 5 steps, you review the diff, run tests, create PR]
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 13. CUSTOM INSTRUCTIONS

<br/>

## Q. How does GitHub Copilot handle context from your codebase?

Copilot uses several techniques to gather relevant context beyond the current file:

**1. Neighboring tabs heuristic**
Open tabs that are semantically related to the current file are included in the context window. Keep related files open for better completions.

**2. Codebase indexing (Enterprise / `@workspace`)**
GitHub Enterprise can index your entire codebase for semantic search. Use `@workspace` in Chat to query it:

```
@workspace How is authentication middleware applied to routes?
@workspace Where is the database transaction wrapper defined?
@workspace Find all places where we call the payment API
```

**3. `.github/copilot-instructions.md`**
A special file that provides persistent context Copilot reads on every chat session:

```markdown
<!-- .github/copilot-instructions.md -->
## Project Context

This is a Node.js 22 + TypeScript 5 + Express 4 REST API.
Database: PostgreSQL 16 via Prisma ORM.
Testing: Vitest (not Jest).
Authentication: JWT with refresh tokens stored in Redis.
Code style: ESLint + Prettier; max line length 100; no semicolons.

## Conventions
- All new routes must include OpenAPI JSDoc annotations.
- Use Result<T, E> pattern for error handling (see src/types/result.ts).
- Services are in src/services/, controllers in src/controllers/.
```

**4. Context window limits by model:**

| Model | Context window |
|-------|---------------|
| GPT-4o | 128K tokens |
| Claude 3.7 Sonnet | 200K tokens |
| Gemini 1.5 Pro | 1M tokens |

**5. Symbols and imports**
Copilot resolves imports and type definitions to understand which APIs are available and how they're used.

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 14. UNIT TEST GENERATION

<br/>

## Q. What are custom instructions for GitHub Copilot?

**Custom instructions** let you define persistent rules that shape every Copilot response — enforcing your code style, frameworks, naming conventions, and architectural patterns without repeating yourself in every prompt.

**Repository-level instructions (`.github/copilot-instructions.md`):**

```markdown
# Copilot Instructions

## Tech Stack
- Runtime: Node.js 22 LTS
- Language: TypeScript 5.x (strict mode)
- Framework: Fastify 5 (NOT Express)
- ORM: Drizzle ORM with PostgreSQL
- Testing: Vitest + Testing Library

## Code Style
- Use `type` instead of `interface` for object shapes
- No default exports — use named exports only
- Prefer `const` arrow functions over `function` declarations
- Error handling: throw `AppError` (see src/errors/AppError.ts)
- Never use `any` type — use `unknown` with type guards

## Patterns
- All async functions must use try/catch with proper error classification
- Repository pattern: services never query the DB directly
- Validate all input at controller level using Zod schemas
```

**User-level instructions (VS Code settings):**

```json
// .vscode/settings.json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "Always add JSDoc comments to public functions and classes."
    },
    {
      "file": ".github/copilot-instructions.md"
    }
  ],
  "github.copilot.chat.testGeneration.instructions": [
    {
      "text": "Use Vitest. Describe blocks named after the class, it blocks describe behavior. Always test edge cases."
    }
  ]
}
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 15. ORGANIZATION ADMINISTRATION

<br/>

## Q. How do you use GitHub Copilot for unit test generation?

Copilot excels at generating unit tests. Use `/tests` slash command, Inline Chat, or Chat with specific instructions.

**Method 1: Slash command**
```
# Select a function/class → open Chat → type:
/tests

# Or with specifics:
/tests using Vitest, test happy path, edge cases, and error scenarios
```

**Method 2: Inline Chat**
```
# Select the function, press Ctrl+I:
"Generate comprehensive Jest unit tests including mocking of dependencies"
```

**Method 3: Chat with file reference**
```
#file:src/services/order.service.ts
Generate unit tests for the OrderService class using Jest and ts-jest.
Mock the OrderRepository and PaymentService dependencies.
Test: createOrder, cancelOrder, getOrderById
Include: success cases, not-found errors, payment failures
```

**Example — Copilot generates tests for this service:**

```typescript
// src/services/cart.service.ts
export class CartService {
  constructor(private readonly cartRepo: CartRepository) {}

  async addItem(userId: string, productId: string, qty: number): Promise<Cart> {
    if (qty <= 0) throw new Error('Quantity must be positive');
    const cart = await this.cartRepo.findOrCreate(userId);
    cart.items.push({ productId, qty });
    return this.cartRepo.save(cart);
  }
}
```

```typescript
// Copilot generates: __tests__/cart.service.test.ts
import { CartService } from '../src/services/cart.service';
import { CartRepository } from '../src/repositories/cart.repository';

jest.mock('../src/repositories/cart.repository');

describe('CartService', () => {
  let cartService: CartService;
  let mockCartRepo: jest.Mocked<CartRepository>;

  beforeEach(() => {
    mockCartRepo = new CartRepository() as jest.Mocked<CartRepository>;
    cartService = new CartService(mockCartRepo);
  });

  describe('addItem', () => {
    it('should add item to cart and save', async () => {
      const mockCart = { items: [] };
      mockCartRepo.findOrCreate.mockResolvedValue(mockCart as any);
      mockCartRepo.save.mockResolvedValue({ ...mockCart, items: [{ productId: 'p1', qty: 2 }] } as any);

      const result = await cartService.addItem('user1', 'p1', 2);

      expect(mockCartRepo.findOrCreate).toHaveBeenCalledWith('user1');
      expect(mockCartRepo.save).toHaveBeenCalled();
      expect(result.items).toHaveLength(1);
    });

    it('should throw when quantity is zero', async () => {
      await expect(cartService.addItem('user1', 'p1', 0))
        .rejects.toThrow('Quantity must be positive');
    });

    it('should throw when quantity is negative', async () => {
      await expect(cartService.addItem('user1', 'p1', -1))
        .rejects.toThrow('Quantity must be positive');
    });
  });
});
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 16. PRIVACY AND DATA

<br/>

## Q. How do you configure GitHub Copilot at the organization level?

GitHub organization admins can centrally control Copilot access, model availability, and security policies.

**1. Enable Copilot for the organization:**

```
GitHub.com → Organization settings → Copilot → Access
→ Enable for: All members / Selected members / Disabled
```

**2. Policy controls (Copilot Business/Enterprise):**

```
Organization settings → Copilot → Policies:
✅ Suggestions matching public code: Block / Allow
✅ Allow use of Copilot Chat: On / Off
✅ Allow use of Copilot in GitHub.com: On / Off
✅ Allow Copilot Extensions: On / Off (specific extensions)
✅ Copilot Workspace: On / Off
```

**3. Manage via GitHub REST API:**

```bash
# List Copilot seat assignments in the org
gh api orgs/{org}/copilot/billing/seats \
  --header "Accept: application/vnd.github+json"

# Add a user to Copilot access
gh api orgs/{org}/copilot/billing/selected_users \
  --method POST \
  --field selected_usernames[]="octocat"

# Remove a user
gh api orgs/{org}/copilot/billing/selected_users \
  --method DELETE \
  --field selected_usernames[]="octocat"

# Get usage metrics (seats used vs assigned)
gh api orgs/{org}/copilot/usage \
  --header "Accept: application/vnd.github+json"
```

**4. Audit logs:**

```bash
# View Copilot-related audit log events
gh api orgs/{org}/audit-log \
  --field phrase="action:copilot" \
  --field per_page=100
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 17. LANGUAGE SUPPORT

<br/>

## Q. What privacy and data considerations apply to GitHub Copilot?

Understanding what data Copilot sends and stores is important for compliance:

**Data sent to GitHub/OpenAI:**
- Code context (surrounding code, open tabs)
- Your prompts in Chat
- File names and paths

**Data retention policies:**

| Plan | Prompt retention | Suggestion retention |
|------|-----------------|---------------------|
| Copilot Individual | 28 days (can opt out) | Not retained |
| Copilot Business | Not retained | Not retained |
| Copilot Enterprise | Not retained | Not retained |

**Key settings for data privacy:**

```json
// .vscode/settings.json
{
  // Opt out of telemetry
  "github.copilot.advanced": {
    "telemetry.enabled": false
  }
}
```

```
# Organization policy: disable training data collection
GitHub.com → Org Settings → Copilot → Policies
→ "Allow GitHub to use my code for product improvements": Off
```

**For regulated industries (HIPAA, GDPR, SOC 2):**

- **Copilot Business/Enterprise**: Prompts and suggestions are NOT used to train the model
- GitHub has a **BAA (Business Associate Agreement)** available for HIPAA compliance (Enterprise)
- Copilot Enterprise keeps data within the GitHub infrastructure (no third-party model providers see your code for Business/Enterprise)
- Use `.copilotignore` or `.gitignore`-style exclusions to prevent Copilot from reading sensitive files:

```gitignore
# .copilotignore — prevent Copilot from reading these files
secrets/
*.pem
*.key
.env*
config/production.json
src/compliance/
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## # 18. ENTERPRISE BEST PRACTICES

<br/>

## Q. How does GitHub Copilot support multiple programming languages?

Copilot works across virtually all programming languages. Quality of suggestions correlates with how well-represented that language is in public training data.

**Tier 1 (Excellent support):**
Python, JavaScript, TypeScript, Java, C#, C++, Go, Ruby, Rust, PHP

**Tier 2 (Good support):**
Kotlin, Swift, Scala, R, MATLAB, Dart, Lua, Perl, Shell/Bash

**Tier 3 (Basic support):**
COBOL, Fortran, Erlang, Haskell, niche DSLs

**Cross-language examples Copilot handles:**

```python
# Python — data processing
# Read a parquet file and return rows where sales > 1000, grouped by region
import pandas as pd

def get_high_sales_by_region(path: str) -> pd.DataFrame:
    df = pd.read_parquet(path)
    return df[df['sales'] > 1000].groupby('region')['sales'].sum().reset_index()
```

```go
// Go — concurrent web scraper
// Fetch URLs concurrently using a worker pool, return results
func fetchAll(urls []string, workers int) []Result {
    jobs := make(chan string, len(urls))
    results := make(chan Result, len(urls))

    for i := 0; i < workers; i++ {
        go func() {
            for url := range jobs {
                resp, err := http.Get(url)
                results <- Result{URL: url, Status: resp.StatusCode, Err: err}
            }
        }()
    }

    for _, url := range urls {
        jobs <- url
    }
    close(jobs)

    var all []Result
    for range urls {
        all = append(all, <-results)
    }
    return all
}
```

```bash
# Bash — find and archive log files older than 30 days
find /var/log/app -name "*.log" -mtime +30 -exec gzip {} \; -exec mv {}.gz /archive/ \;
```

```sql
-- SQL — find customers who bought in consecutive months
SELECT customer_id
FROM orders
GROUP BY customer_id
HAVING COUNT(DISTINCT DATE_TRUNC('month', order_date)) = 
       DATEDIFF('month', MIN(order_date), MAX(order_date)) + 1
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>

## Q. What are GitHub Copilot best practices for enterprise teams?

**1. Set up repository-level custom instructions**

```markdown
<!-- .github/copilot-instructions.md -->
Always use company-approved libraries only (see APPROVED_LIBRARIES.md).
Never suggest storing secrets in code — use Azure Key Vault references.
Follow the error handling pattern in src/shared/errors.ts.
All database queries must go through the Repository layer.
```

**2. Create a `.copilotignore` for sensitive files**

```gitignore
# .copilotignore
.env*
secrets/
certs/
**/migrations/*.sql   # prevent leaking schema to completions
internal/compliance/
```

**3. Use prompt files / reusable prompts**

```markdown
<!-- .github/prompts/add-endpoint.prompt.md -->
Create a new REST endpoint following these rules:
1. Route in src/routes/ with OpenAPI annotation
2. Controller in src/controllers/ (no business logic)
3. Service in src/services/ (all business logic)
4. Zod validation schema in src/schemas/
5. Unit tests in __tests__/ with 80%+ coverage
6. Update the Swagger docs in openapi.yaml
```

**4. Standardize keyboard shortcuts across the team**

```json
// Share this keybindings.json via Settings Sync or team onboarding docs
[
  { "key": "ctrl+shift+i", "command": "github.copilot.edits.attachContext" },
  { "key": "ctrl+i",       "command": "github.copilot.inlineChat.start" },
  { "key": "ctrl+alt+i",   "command": "workbench.panel.chat.view.copilot.focus" }
]
```

**5. Integrate Copilot into CI/CD**

```yaml
# .github/workflows/copilot-autofix.yml
name: Copilot Autofix
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    permissions:
      security-events: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - name: Run CodeQL
        uses: github/codeql-action/analyze@v3
        # Copilot Autofix will automatically suggest fixes for detected issues
```

**6. Measure adoption with usage metrics**

```bash
# Monthly Copilot usage report for the org
gh api orgs/{org}/copilot/usage \
  --header "Accept: application/vnd.github+json" | \
  jq '[.[] | {date: .day, accepted: .total_acceptances_count, suggested: .total_suggestions_count, acceptance_rate: (.total_acceptances_count / .total_suggestions_count * 100 | round)}]'
```

<div align="right">
    <b><a href="#table-of-contents">↥ back to top</a></b>
</div>
