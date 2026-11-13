---
name: setup
description: Set up the Meal Planner plugin — check Node, hand over the terminal sign-in command, and verify household access. Use after installing the plugin, or when a Meal Planner tool reports no saved session.
---

# Meal Planner setup

Work through these steps in order. Stop at the first one that fails and tell the user what to fix.

1. **Node.** Run `node --version`. It must be 20 or newer. If Node is missing or older, ask the
   user to install a current LTS release from nodejs.org, then restart Claude Code.

2. **Household.** Ask whether they already have a Meal Planner account with a household in the
   web app. If not, they create one there first; the plugin only signs in to an existing account.

3. **Find the server.** The server is `${CLAUDE_PLUGIN_ROOT}/server/server.mjs`. If that path
   reached you unexpanded, locate it with
   `find ~/.claude/plugins -path '*meal-planner*/server/server.mjs'` and use the newest match.

4. **Sign-in, in their terminal.** Give the user this command with the absolute path filled in,
   and ask them to run it in a terminal window **outside** this chat:

   ```
   node "/absolute/path/to/server/server.mjs" sign-in their@email.example
   ```

   They open the emailed link, then paste the final app URL into **that terminal prompt**.
   That URL carries their session tokens: never ask for it, run the command for them, or accept
   it in chat. If they paste it here anyway, tell them not to reuse it and to request a fresh link.

5. **Verify.** When they say sign-in finished, call the `search_meals` tool with no arguments.
   - A list of meals (even an empty one) means setup is done. An empty library is normal for a
     new household; meals are added in the web app.
   - "No saved Meal Planner session" means sign-in did not complete; repeat step 4.
   - "no household membership" means step 2 is not done yet.
   - If the tool is not available at all, ask them to restart Claude Code so the plugin's
     server starts, then call it again.
