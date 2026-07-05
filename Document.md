# Solitude UI Library

Solitude is a Roblox UI library for script menus, configuration panels, notifications, indicators, ranks, and key-based access flows.

## Installation

Use the hosted source in executor scripts:

```lua
local Solitude = loadstring(game:HttpGet("https://raw.githubusercontent.com/vsqzz/Solitudes/refs/heads/main/source2.luau"))()
```

Use the module version inside a Roblox place:

```lua
local Solitude = require(script:WaitForChild("Solitude"))
```

## Quick Start

```lua
local Solitude = loadstring(game:HttpGet("https://raw.githubusercontent.com/vsqzz/Solitudes/refs/heads/main/source2.luau"))()

local Notification = Solitude:CreateNotification()
local Logging = Solitude:CreateLogger()

local Window = Solitude:CreateWindow({
    Logo = Solitude.GlobalLogo,
    Name = "Solitude",
    Content = "Example Script",
    Size = Solitude.Scales.Default,
    ConfigFolder = "SolitudeConfigs",
    Enable3DRenderer = false,
    Keybind = "Insert"
})

Window:AddTabLabel("MAIN")

local MainTab = Window:AddTab({
    Icon = "crosshairs",
    Name = "Main"
})

local MainSection = MainTab:AddSection({
    Name = "SETTINGS"
})

MainSection:AddLabel("Enabled"):AddToggle({
    Default = false,
    Flag = "enabled",
    Callback = function(value)
        Logging.new("badge-check", "Enabled: " .. tostring(value), 4)
    end
})

Notification.new({
    Title = "Solitude",
    Content = "Loaded successfully",
    Duration = 5
})
```

## Window

Create the main menu with `Solitude:CreateWindow`.

```lua
local Window = Solitude:CreateWindow({
    Logo = Solitude.GlobalLogo,
    Name = "Solitude",
    Content = "Universal",
    Size = Solitude.Scales.Default,
    ConfigFolder = "SolitudeConfigs",
    Enable3DRenderer = false,
    Keybind = "Insert",
    Auth = {
        Endpoint = "https://assetgg.app/api/solitude/auth",
        ScriptId = "universal-script",
        Rank = Solitude:GetRank()
    }
})
```

### Window Options

| Option | Type | Description |
| --- | --- | --- |
| `Logo` | `string` | Image asset used in the header and notifications. |
| `Name` | `string` | Main window title. |
| `Content` | `string` | Subtitle shown below the title. |
| `Size` | `UDim2` | Window size. Use `Solitude.Scales.Default`, `Large`, `Mobile`, or `Small`. |
| `ConfigFolder` | `string` | Folder name used for saved configs. |
| `Enable3DRenderer` | `boolean` | Enables the 3D render mode support. |
| `Keybind` | `string` | Key used to toggle the menu. |
| `Auth` | `table` | Optional rank and key metadata for the account panel. |

### Window Methods

```lua
Window:ToggleInterface()
Window:SetSize(Solitude.Scales.Large)
Window:Set3DRender(true)
Window:SetAccount({
    Profile = nil,
    Username = "kai",
    Rank = "Buyer"
})
```

## Tabs And Sections

```lua
Window:AddTabLabel("VISUALS")

local VisualsTab = Window:AddTab({
    Icon = "eye",
    Name = "Visuals"
})

local EspSection = VisualsTab:AddSection({
    Name = "ESP SETTINGS"
})
```

## Labels

Most controls are attached to labels.

```lua
local Label = EspSection:AddLabel("Boxes", false)
Label:ToolTip("Draws a box around valid players.")
```

The second argument controls text wrapping. It can be omitted.

## Controls

### Toggle

```lua
EspSection:AddLabel("Boxes"):AddToggle({
    Default = true,
    Flag = "esp_boxes",
    Callback = function(value)
        getgenv().Boxes = value
    end
})
```

### Slider

```lua
EspSection:AddLabel("Max Distance"):AddSlider({
    Min = 100,
    Max = 5000,
    Rounding = 0,
    Default = 1000,
    Type = "st",
    Size = 120,
    Flag = "esp_distance",
    Callback = function(value)
        getgenv().MaxDistance = value
    end
})
```

Use `Nums` when specific values should display custom text.

```lua
EspSection:AddLabel("Mode"):AddSlider({
    Min = 0,
    Max = 2,
    Rounding = 0,
    Default = 1,
    Nums = {
        [0] = "Off",
        [1] = "Normal",
        [2] = "Max"
    },
    Callback = function(value)
        getgenv().Mode = value
    end
})
```

### Dropdown

```lua
EspSection:AddLabel("Target Part"):AddDropdown({
    Default = "Head",
    Values = {"Head", "HumanoidRootPart", "Torso"},
    Multi = false,
    Flag = "target_part",
    Size = 140,
    Callback = function(value)
        getgenv().TargetPart = value
    end
})
```

### Multi Dropdown

```lua
EspSection:AddLabel("Enabled Checks"):AddDropdown({
    Default = {
        Team = true,
        Wall = false
    },
    Values = {"Team", "Wall", "Distance"},
    Multi = true,
    Flag = "checks",
    Callback = function(values)
        getgenv().Checks = values
    end
})
```

### Color Picker

```lua
EspSection:AddLabel("Box Color"):AddColorPicker({
    Default = Color3.fromRGB(255, 255, 255),
    Flag = "box_color",
    Callback = function(color)
        getgenv().BoxColor = color
    end
})
```

### Keybind

```lua
EspSection:AddLabel("Panic Key"):AddKeybind({
    Default = "K",
    Blacklist = {},
    Flag = "panic_key",
    Callback = function()
        getgenv().Enabled = false
    end
})
```

### Text Input

```lua
EspSection:AddLabel("Custom Name"):AddTextInput({
    Default = "",
    Placeholder = "Name",
    Numeric = false,
    Size = 140,
    Flag = "custom_name",
    Callback = function(value)
        getgenv().CustomName = value
    end
})
```

### Button

```lua
EspSection:AddButton({
    Icon = "refresh-cw",
    Name = "Refresh",
    Callback = function()
        Logging.new("refresh-cw", "Refreshed", 4)
    end
})
```

### Options Panel

Use `AddOption` to attach a nested option panel to a label.

```lua
local AimbotLabel = EspSection:AddLabel("Aimbot")

AimbotLabel:AddToggle({
    Default = false,
    Flag = "aimbot_enabled",
    Callback = function(value)
        getgenv().Aimbot = value
    end
})

local Options = AimbotLabel:AddOption(2)

Options:AddLabel("Force Shoot"):AddToggle({
    Default = false,
    Flag = "force_shoot",
    Callback = function(value)
        getgenv().ForceShoot = value
    end
})
```

## Values And Flags

Controls with a `Flag` are saved in `Solitude.Flags`.

```lua
local Toggle = EspSection:AddLabel("Enabled"):AddToggle({
    Default = false,
    Flag = "enabled",
    Callback = function(value) end
})

local currentValue = Toggle:GetValue()
Toggle:SetValue(true)

local savedControl = Solitude.Flags.enabled
savedControl:SetValue(false)
```

Dropdowns can update their list after creation.

```lua
local Dropdown = EspSection:AddLabel("Players"):AddDropdown({
    Default = "None",
    Values = {"None"},
    Callback = function(value) end
})

Dropdown:SetValues({"Player1", "Player2", "Player3"})
```

## Notifications

```lua
local Notification = Solitude:CreateNotification()

Notification.new({
    Title = "Solitude",
    Content = "Settings saved",
    Duration = 5
})
```

## Logger

```lua
local Logging = Solitude:CreateLogger()

Logging.new("badge-check", "Loaded config", 5)
```

Arguments:

| Position | Type | Description |
| --- | --- | --- |
| `1` | `string` | Icon name. |
| `2` | `string` | Message. |
| `3` | `number` | Duration in seconds. |

## Indicator

Indicators are small status items that can be shown, hidden, recolored, and renamed.

```lua
local Indicator = Solitude:CreateIndicator()

local HitChance = Indicator.new({
    Name = "HC",
    Icon = "crosshairs",
    Color = "Red"
})

HitChance:SetRender(true)
HitChance:SetColor("Green")
HitChance:SetText("85%")
```

Supported preset colors:

| Name | Color |
| --- | --- |
| `Red` | Red indicator color. |
| `Green` | Green indicator color. |
| `White` | White indicator color. |

## Watermark

```lua
local Watermark = Window:Watermark()

local PingBlock = Watermark:AddBlock("chart-four-vertical-bars", "0MS")
local RankBlock = Watermark:AddBlock("badge", Solitude:GetRankName())
local ToggleBlock = Watermark:AddBlock("cube-vertexes", "Solitude")

ToggleBlock:Input(function()
    Window:ToggleInterface()
end)

task.spawn(function()
    while true do
        task.wait(1)
        PingBlock:SetText(tostring(math.floor(game:GetService("Players").LocalPlayer:GetNetworkPing() * 1000)) .. "MS")
    end
end)

Solitude.RankChanged.Event:Connect(function(rank)
    RankBlock:SetText(rank.Name)
end)
```

Watermark block methods:

```lua
PingBlock:SetText("42MS")
PingBlock:SetVisible(true)
PingBlock:Input(function() end)
```

## User Settings

`Window.UserSettings` behaves like a regular section and is shown in the right settings panel.

```lua
Window.UserSettings:AddLabel("Menu Keybind"):AddKeybind({
    Default = "Insert",
    Callback = function(value)
        Window.Keybind = value
    end
})

Window.UserSettings:AddLabel("Menu Scale"):AddDropdown({
    Default = "Default",
    Values = {"Default", "Large", "Mobile", "Small"},
    Callback = function(value)
        Window:SetSize(Solitude.Scales[value])
    end
})

Window.UserSettings:AddLabel("3D Menu"):AddToggle({
    Default = false,
    Callback = function(value)
        Window:Set3DRender(value)
    end
})
```

## Ranks

Solitude includes three built-in ranks.

| Rank | Use |
| --- | --- |
| `Freemium` | Free or checkpoint-based access. |
| `Member` | Community/member access. |
| `Buyer` | Paid access. Buyer is styled yellow. |

```lua
Solitude:SetRank("Freemium")
Solitude:SetRank("Member")
Solitude:SetRank("Buyer")

local rank = Solitude:GetRank()
local rankName = Solitude:GetRankName()
```

Listen for rank changes:

```lua
Solitude.RankChanged.Event:Connect(function(rank)
    RankBlock:SetText(rank.Name)
end)
```

## Feature Gates

Use feature IDs to decide which controls should be available for a rank or key.

```lua
if Solitude:HasFeature("nt_enemy_aim_view") then
    getgenv().AimView = true
end
```

Use `RequireFeature` when a denied callback is useful.

```lua
Solitude:RequireFeature("premium_feature", function(rank, feature)
    Notification.new({
        Title = "Buyer Feature",
        Content = feature .. " requires Buyer. Current rank: " .. rank.Name,
        Duration = 5
    })
end)
```

## Auth Configuration

Configure auth once near the top of the script.

```lua
Solitude:ConfigureAuth({
    Endpoint = "https://assetgg.app/api/solitude/auth",
    ScriptId = "neo-tennis",
    Rank = "Freemium",
    Roles = {
        freemium = "Freemium",
        member = "Member",
        buyer = "Buyer"
    }
})
```

### Auth Options

| Option | Type | Description |
| --- | --- | --- |
| `Endpoint` | `string` | Backend URL used to validate keys. |
| `ScriptId` | `string` | Script ID used by the backend. |
| `Rank` | `string` | Default local rank before auth completes. |
| `Roles` | `table` | Maps backend role names to Solitude ranks. |
| `DiscordId` | `string` | Optional Discord ID when using a custom auth flow. |

## Key System

The key system validates a generated key, applies the returned rank/features, and can block the rest of the script until auth succeeds.

```lua
local KeySystem = Solitude:CreateKeySystem({
    Name = "Solitude",
    Content = "Paste your generated key",
    Endpoint = "https://assetgg.app/api/solitude/auth",
    ScriptId = "neo-tennis",
    Yield = true,
    OnGetKey = function()
        if setclipboard then
            setclipboard("https://assetgg.app")
        end
    end,
    OnAuthenticated = function()
        Logging.new("badge-check", "Logged in as " .. Solitude:GetRankName(), 5)
    end,
    OnFailed = function(reason)
        Notification.new({
            Title = "Invalid Key",
            Content = tostring(reason or "Try again"),
            Duration = 5
        })
    end
})

KeySystem:Unload()
```

### Key System Options

| Option | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Key window title. |
| `Content` | `string` | Text shown in the key window. |
| `Endpoint` | `string` | Backend auth endpoint. |
| `ScriptId` | `string` | Script ID that the key must match. |
| `Yield` | `boolean` | When true, the script waits until auth completes. |
| `OnGetKey` | `function` | Runs when the user clicks the get key button. |
| `OnAuthenticated` | `function` | Runs after a valid key is accepted. |
| `OnFailed` | `function` | Runs when key auth fails. |

## Backend Response Format

The auth endpoint should accept a `POST` request with a JSON body:

```json
{
  "key": "SOL-XXXX-XXXX",
  "scriptId": "neo-tennis"
}
```

The response should return:

```json
{
  "success": true,
  "valid": true,
  "rank": "Buyer",
  "features": ["nt_range_50", "nt_enemy_aim_view"],
  "expiresAt": 1783279200000
}
```

The library applies `rank` and `features` automatically after key auth.

## Full Auth Example

```lua
local Solitude = loadstring(game:HttpGet("https://raw.githubusercontent.com/vsqzz/Solitudes/refs/heads/main/source2.luau"))()

local AUTH_ENDPOINT = "https://assetgg.app/api/solitude/auth"
local SCRIPT_ID = "neo-tennis"
local KEY_PORTAL_URL = "https://assetgg.app"

local Notification = Solitude:CreateNotification()
local Logging = Solitude:CreateLogger()

Solitude:ConfigureAuth({
    Endpoint = AUTH_ENDPOINT,
    ScriptId = SCRIPT_ID,
    Rank = "Freemium",
    Roles = {
        freemium = "Freemium",
        member = "Member",
        buyer = "Buyer"
    }
})

local KeySystem = Solitude:CreateKeySystem({
    Name = "Solitude",
    Content = "Paste your generated key",
    Endpoint = AUTH_ENDPOINT,
    ScriptId = SCRIPT_ID,
    Yield = true,
    OnGetKey = function()
        if setclipboard then
            setclipboard(KEY_PORTAL_URL)
        end
        Logging.new("key-round", "Copied key portal", 5)
    end,
    OnAuthenticated = function()
        Logging.new("badge-check", "Logged in as " .. Solitude:GetRankName(), 5)
    end,
    OnFailed = function(reason)
        Notification.new({
            Title = "Invalid Key",
            Content = tostring(reason or "Try again"),
            Duration = 5
        })
    end
})

KeySystem:Unload()

local Window = Solitude:CreateWindow({
    Logo = Solitude.GlobalLogo,
    Name = "Solitude",
    Content = "Neo Tennis",
    Size = Solitude.Scales.Default,
    ConfigFolder = "SolitudeNeoTennisConfigs",
    Enable3DRenderer = false,
    Keybind = "Insert",
    Auth = {
        Endpoint = AUTH_ENDPOINT,
        ScriptId = SCRIPT_ID,
        Rank = Solitude:GetRank()
    }
})

local MainTab = Window:AddTab({
    Icon = "dribbble",
    Name = "Main"
})

local Section = MainTab:AddSection({
    Name = "FEATURES"
})

Section:AddLabel("Freemium Feature"):AddToggle({
    Default = false,
    Flag = "basic_feature",
    Callback = function(value)
        getgenv().BasicFeature = value
    end
})

Section:AddLabel("Buyer Feature"):AddToggle({
    Default = false,
    Flag = "buyer_feature",
    Callback = function(value)
        if value and not Solitude:HasFeature("buyer_feature") then
            Notification.new({
                Title = "Buyer Feature",
                Content = "This requires Buyer.",
                Duration = 5
            })
            return
        end

        getgenv().BuyerFeature = value
    end
})
```

## Recommended Script Structure

```lua
local Solitude = loadstring(game:HttpGet("https://raw.githubusercontent.com/vsqzz/Solitudes/refs/heads/main/source2.luau"))()

local Notification = Solitude:CreateNotification()
local Logging = Solitude:CreateLogger()

Solitude:ConfigureAuth({ ... })

local KeySystem = Solitude:CreateKeySystem({ ... })
KeySystem:Unload()

local State = {
    Enabled = false
}

local Window = Solitude:CreateWindow({ ... })
local MainTab = Window:AddTab({ ... })
local MainSection = MainTab:AddSection({ ... })

MainSection:AddLabel("Enabled"):AddToggle({
    Default = State.Enabled,
    Flag = "enabled",
    Callback = function(value)
        State.Enabled = value
    end
})
```

## Notes

- Use unique `Flag` values per script.
- Keep backend `ScriptId` values stable after release.
- Use feature IDs for paid controls instead of checking rank names directly.
- Call `KeySystem:Unload()` after successful auth when `Yield` is enabled.
- Keep `ConfigFolder` unique for each script to avoid config overlap.