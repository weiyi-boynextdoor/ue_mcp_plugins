# UE MCP Plugins

A UE5 project for testing and developing MCP plugins.

## Enable MCP

1. In **Edit > Plugins**, enable **Unreal MCP** (`ModelContextProtocol`) and restart the editor if prompted. It is already enabled in this project's `.uproject` file.
2. Open the **Output Log** and run this console command to start the MCP server:

   ```text
   ModelContextProtocol.StartServer <server_port_8000_as_default>
   ```

3. Generate MCP configuration files for all supported clients:

   ```text
   ModelContextProtocol.GenerateClientConfig All
   ```

   This generates `.mcp.json` (Claude Code), `.cursor/mcp.json`, `.vscode/mcp.json`, `.gemini/settings.json`, and `.codex/config.toml` (Codex uses TOML). Source builds write these files to the engine workspace root (the directory containing `Engine/`); installed builds write them to the project directory. Check the Output Log for the generated paths.
