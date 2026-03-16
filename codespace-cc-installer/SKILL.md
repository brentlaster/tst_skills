---
name: codespace-cc-installer
description: >
  Automates the full installation and authentication of Claude Code inside a running GitHub Codespace
  in Chrome. Use this skill whenever the user wants to install Claude Code in a Codespace, set up
  Claude Code in a Codespace, or says things like "install claude in the codespace", "set up claude code
  in the codespace", "get claude running in the codespace", or provides a Codespace URL and wants
  Claude Code installed and authenticated. Also trigger when the user mentions installing the Claude CLI,
  setting up the Claude terminal, or authenticating Claude in a browser-based VS Code environment.
  This skill drives Chrome to type commands in the Codespace terminal, handle the OAuth authentication
  flow across tabs, and complete the interactive setup prompts.
---
 
# Codespace Claude Code Installer
 
You are a browser automation agent that installs and authenticates Claude Code inside a running GitHub Codespace through Chrome. The user will point you at a Chrome tab containing an already-running Codespace with a visible terminal. Your job is to execute the entire installation and authentication flow end-to-end.
 
## Before You Start
 
1. **Get the Codespace tab** — use `tabs_context_mcp` to discover the Chrome tab containing the running Codespace. If the user provided a URL, navigate to it. The Codespace should already be fully loaded with a visible terminal.
2. **Take a screenshot** to confirm you can see VS Code with a terminal panel.
3. **Click on the terminal** to ensure it has focus before typing anything.
 
## Phase 1: Install Claude Code
 
### Step 1: Install via npm
 
GitHub Codespaces come with Node.js pre-installed, so npm is available. Type the install command in the terminal:
 
```
npm install -g @anthropic-ai/claude-code
```
 
Press Enter and wait for the installation to complete. This typically takes 30-60 seconds. You'll know it's done when you see a clean command prompt again (the `$` prompt returns with no active process).
 
**How to monitor progress:** Take screenshots every 15-20 seconds to check progress. Look for:
- npm download/install progress output
- Any error messages (especially EACCES permission errors)
- The return of a clean command prompt indicating completion
 
**If you see permission errors (EACCES):** Try the alternative install approach:
```
curl -fsSL https://claude.ai/install.sh | bash
```
After the curl install finishes, you may need to source the profile or open a new terminal for the `claude` command to be on the PATH. Try `source ~/.bashrc` first, or if that doesn't help, open a new terminal with Ctrl+Shift+`.
 
### Step 2: Launch Claude Code
 
Once installation is complete, type:
 
```
claude --dangerously-skip-permissions
```
 
Press Enter. Claude Code will start up — this takes a few seconds. Wait until you see output on screen before proceeding.
 
## Phase 2: Interactive Setup
 
Claude Code will present several interactive prompts. Handle each one in order. After each selection, wait a moment for the next prompt to appear before proceeding.
 
### Step 3: Theme selection
 
Claude Code first asks to choose a theme. Type `1` and press Enter to select **Dark mode**.
 
Wait for the next prompt to appear.
 
### Step 4: Login method selection
 
Claude Code asks how to authenticate. Type `1` and press Enter to select **Claude account with subscription**.
 
### Step 5: Dismiss the authentication popup
 
After selecting the login method, Claude Code will attempt to open a browser popup/dialog with an authentication URL. This popup doesn't work properly inside a Codespace environment.
 
**Dismiss the popup** — look for a dialog or notification and click "Cancel", "Dismiss", or the X button to close it. Take a screenshot to find the dismiss/cancel control. The popup may appear as:
- A VS Code notification in the bottom-right
- A modal dialog
- A browser permission dialog
 
Click whatever dismiss/cancel option is available. The important thing is to close this popup so you can get back to the terminal.
 
### Step 6: Copy the authentication URL from the terminal
 
After dismissing the popup, look at the terminal output. Claude Code will have printed a full authentication URL. This URL is what you need to authenticate.
 
**Read the terminal text** to find the URL. It will look something like:
```
https://claude.ai/oauth/authorize?code_challenge=...&client_id=...
```
 
Use `read_page` or `get_page_text` to extract the URL text from the terminal, or take a screenshot and read it carefully. The URL is long — make sure you capture the complete URL including all query parameters.
 
**Important:** The URL must be copied exactly and completely. If you can't read it from a screenshot, try selecting the text in the terminal. You can triple-click to select a line, or use the terminal's selection mechanism.
 
### Step 7: Open the authentication URL in a new tab
 
Create a new Chrome tab using `tabs_create_mcp`, then navigate to the authentication URL you captured.
 
Wait for the page to load fully.
 
### Step 8: Authorize Claude Code
 
On the authentication page, find and click the **"Authorize"** button. Wait a moment for the page to process.
 
### Step 9: Copy the authorization code
 
After clicking Authorize, the page will display a code. Find and click the **"Copy Code"** button to copy the authorization code to the clipboard.
 
**Alternative if "Copy Code" button is hard to find:** Read the code directly from the page text using `read_page` or `get_page_text`, then you can type it manually in the terminal.
 
### Step 10: Return to the Codespace terminal
 
Switch back to the Codespace tab (use the tab ID from earlier). Click on the terminal area to ensure it has focus.
 
### Step 11: Paste the authorization code
 
The terminal should be showing a prompt like "Paste code here if prompted" or similar. Paste the authorization code:
 
- Use Ctrl+V to paste, or
- Type the code directly if clipboard paste doesn't work
 
Press Enter after pasting/typing the code.
 
### Step 12: Continue through the post-auth prompts
 
After entering the code, Claude Code will display some information. You'll need to press Enter to continue through informational screens:
 
1. **First "Press Enter to continue"** — press Enter
2. **Second "Press Enter to continue"** — press Enter
 
Wait briefly between each press for the screen to update.
 
### Step 13: Answer the three setup questions
 
Claude Code asks three configuration questions in sequence. Answer each one:
 
1. **"Use Claude Code's terminal setup?"** — Type `1` and press Enter (selects "Yes, use recommended settings")
2. **"Security guide" / trust this folder** — Type `1` and press Enter (selects "Yes, I trust this folder")
3. **"Bypass Permissions mode"** — Type `2` and press Enter (selects "Yes, I accept")
 
Wait a moment between each answer for the next question to appear.
 
### Step 14: Wait for Claude Code to fully start
 
After answering the setup questions, Claude Code will display a welcome message and some initialization output. Wait for the output to finish scrolling — you'll know it's done when you see Claude's input prompt (typically a `>` or similar prompt indicator).
 
Press Enter one final time to clear any remaining startup output and get to a clean Claude prompt.
 
## Completion
 
Take a final screenshot to confirm Claude Code is running and ready to accept input. The terminal should show Claude's interactive prompt.
 
Let the user know that Claude Code is installed, authenticated, and ready to use in their Codespace.
 
## Troubleshooting
 
### "command not found" after npm install
The npm global bin path may not be in the shell's PATH. Try:
```bash
export PATH="$HOME/.npm-global/bin:$PATH"
```
Or open a new terminal with Ctrl+Shift+` and try `claude --dangerously-skip-permissions` again.
 
### Authentication URL is too long to read from screenshot
Use `get_page_text` on the Codespace tab to extract terminal text, then search for the URL starting with `https://claude.ai/`. You can also try selecting text in the terminal — click at the start of the URL, then Shift+click at the end.
 
### Popup won't dismiss
Try pressing Escape, or look for a small X button. If the popup is a VS Code notification (bottom-right), there's usually a close icon. If it's a browser-level dialog, look for Cancel/Don't Allow/Block options.
 
### Paste doesn't work in terminal
Instead of Ctrl+V, try:
- Right-click in the terminal and select Paste
- Use Ctrl+Shift+V (VS Code terminal paste shortcut)
- Type the authorization code character by character as a last resort
 
### Terminal lost focus
Click directly on the terminal text area (not the terminal header or tabs) to regain focus before typing.
 
