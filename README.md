# Setting Up MCP server 

This is for windows setup . You need python and uv installed on machine.If
uv not installed then install using 

  ```pip install uv```

Make a ```demo``` folder in windows

```bash
mkdir demo
cd demo
```
Initialized virtual environment

```bash
demo>uv venv
demo>.venv\Scripts\activate
(demo) c:\demo>uv add mcp[cli] requests
```
open the folder in code
```
```bash
(demo) demo>code .
```



## Add Folder src and chess

 With in VS code , click on folder icon and add folder named ``` src```
 Inside the src folder add another folder named ``` chess```

 ## Add File __init__.py 
 Add empty file inside chess folder ```__init__.py```

 ## Add chess_api.py
    Add file named ```chess_api.py``` inside chess folder and add below code


```python
import requests

CHESS_API_BASE = "https://api.chess.com/pub"
headers = {
    "accept": "application/json",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Python/3.10"
}

def get_player_profile(username):
    url = f"{CHESS_API_BASE}/player/{username}"
    response = requests.get(url, headers=headers)
    response.raise_for_status()
    return response.json()

def get_player_stats(username):
    url = f"{CHESS_API_BASE}/player/{username}/stats"
    response = requests.get(url, headers=headers)
    response.raise_for_status()
    return response.json()
```


## Add file server.py
 Add another file named ```server.py``` inside chess folder and add below code



```python
from mcp.server.fastmcp import FastMCP
mcp =  FastMCP("chess.com")

from .chess_api import get_player_profile, get_player_stats

@mcp.tool()
def get_chess_player_profile(username: str):
    """Get the public profile for a Chess.com player by username."""
    return get_player_profile(username)

@mcp.tool()
def get_chess_player_stats(username: str):
    """Get the stats for a Chess.com player by username."""
    return get_player_stats(username)

def main():
    mcp.run(transport="stdio")

if __name__ == "__main__":
    main()
```

## Update the pyproject.toml file

The ```chess.server:main``` indicates there is ```chess``` folder inside ```src```
and call main method in file ```server.py```
```toml

[project.scripts]
chess = "chess.server:main"

[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"

[tool.setuptools]
package-dir = {"" = "src"}

[tool.setuptools.packages.find]
where = ["src"]
```

## Run with MCP
```bash
demo>uv run chess
```
Note the command ```chess``` match  ```[project.scripts]```

There will be no output , if you get any syntax errors it will show. Just
exit out of server console using ```Ctrl+C```

## Install Claude Desktop from official website 

## Add configuration file for Claude

Go to the folder ```C:\Users\{yourname}\AppData\Roaming```. Or In run command type
 ```%APPDATA%``` .Verify that the folder contains a subfolder named ```Claude```.
 ## create a new file name ```claude_desktop_config.json```

add following contents into it . Update the driectory path based on your computer
eg ```C:\\temp\\AI\\MCP_ORL3\\demo```

```json

{
  "mcpServers": {
    "kannan-server": {
      "command": "uv",
      "args": [
        "--directory",
        "C:\\temp\\AI\\MCP_ORL3\\demo",
        "run",
        "chess"
      ]
    }
  }
}
```

## Restart Claude Desktop ,start a new chat 
## Look for icon SearchAndTools below prompt  textbox
  You will be able to see the mcp server agent named ```kannan-server``` ,based on the
  name you provided in the configuration file.

  Note to disable websearch. Now you can start searching for profile info and stats for Chess.com players using the commands you defined.