--[[
    ╔══════════════════════════════════════════════════════════╗
    ║          LÉO'S CRAZY GUI — EXECUTOR SCRIPT EDITION       ║
    ║                  Dandy's World Hub                       ║
    ║              Theme: Black / Neon Purple / Red            ║
    ╚══════════════════════════════════════════════════════════╝

    AVISO: Este script é fornecido apenas para fins educacionais
    em ambientes autorizados (testes privados, jogos próprios).
    Use apenas onde você tem permissão explícita.
--]]

--// Serviços
local Players            = game:GetService("Players")
local RunService         = game:GetService("RunService")
local UserInputService   = game:GetService("UserInputService")
local TweenService       = game:GetService("TweenService")
local CoreGui            = game:GetService("CoreGui")
local Lighting           = game:GetService("Lighting")

local LocalPlayer        = Players.LocalPlayer

--// Configurações de Tema
local Theme = {
    Background      = Color3.fromRGB(10, 10, 14),
    Secondary       = Color3.fromRGB(18, 16, 24),
    Tertiary        = Color3.fromRGB(26, 22, 34),
    NeonPurple      = Color3.fromRGB(160, 60, 255),
    NeonPurpleDark  = Color3.fromRGB(90, 30, 160),
    AccentRed       = Color3.fromRGB(220, 40, 60),
    Text            = Color3.fromRGB(235, 230, 245),
    TextDim         = Color3.fromRGB(150, 140, 170),
    Stroke          = Color3.fromRGB(60, 40, 90),
    Success         = Color3.fromRGB(80, 220, 140),
    Warning         = Color3.fromRGB(255, 200, 60),
}

--// Estado Global
local State = {
    Minimized     = false,
    ActiveTab     = "Home",
    Toggles       = {},
    Sliders       = {},
    Keybinds      = {},
    Modules       = {
        AutoFarm   = false,
        ESP        = false,
        Speed      = false,
        Jump       = false,
        Aimbot     = false,
    },
}

--// Remover GUI anterior
pcall(function()
    if CoreGui:FindFirstChild("LeosCrazyGUI") then
        CoreGui.LeosCrazyGUI:Destroy()
    end
    if game:GetService("CoreGui"):FindFirstChild("LeosCrazyGUI") then
        game:GetService("CoreGui").LeosCrazyGUI:Destroy()
    end
end)

--// Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LeosCrazyGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.IgnoreGuiInset = true

local parentGui = (RunService:IsStudio() and LocalPlayer:WaitForChild("PlayerGui")) or CoreGui
ScreenGui.Parent = parentGui

------------------------------------------------------------
-- UTILITÁRIOS
------------------------------------------------------------
local function new(class, props)
    local obj = Instance.new(class)
    for k, v in pairs(props or {}) do
        obj[k] = v
    end
    return obj
end

local function corner(parent, radius)
    return new("UICorner", {
        CornerRadius = UDim.new(0, radius or 8),
        Parent = parent,
    })
end

local function stroke(parent, color, thickness, transparency)
    return new("UIStroke", {
        Color = color or Theme.Stroke,
        Thickness = thickness or 1,
        Transparency = transparency or 0,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
        Parent = parent,
    })
end

local function gradient(parent, c1, c2, rotation)
    return new("UIGradient", {
        Color = ColorSequence.new(c1, c2),
        Rotation = rotation or 90,
        Parent = parent,
    })
end

local function padding(parent, px)
    return new("UIPadding", {
        PaddingTop = UDim.new(0, px),
        PaddingBottom = UDim.new(0, px),
        PaddingLeft = UDim.new(0, px),
        PaddingRight = UDim.new(0, px),
        Parent = parent,
    })
end

local function tween(obj, time, goal, style, dir)
    local t = TweenService:Create(
        obj,
        TweenInfo.new(time or 0.25, style or Enum.EasingStyle.Quad, dir or Enum.EasingDirection.Out),
        goal
    )
    t:Play()
    return t
end

------------------------------------------------------------
-- JANELA PRINCIPAL
------------------------------------------------------------
local Main = new("Frame", {
    Name = "Main",
    Parent = ScreenGui,
    BackgroundColor3 = Theme.Background,
    BorderSizePixel = 0,
    Size = UDim2.new(0, 560, 0, 400),
    Position = UDim2.new(0.5, -280, 0.5, -200),
    ClipsDescendants = true,
    Active = true,
})
corner(Main, 14)
local mainStroke = stroke(Main, Theme.NeonPurple, 1.5, 0.2)
local mainGradient = gradient(Main, Theme.Background, Color3.fromRGB(20, 14, 32), 45)

-- Sombra externa (brilho neon)
local glow = new("ImageLabel", {
    Name = "Glow",
    Parent = Main,
    BackgroundTransparency = 1,
    Image = "rbxassetid://5028857084",
    ImageColor3 = Theme.NeonPurple,
    ImageTransparency = 0.6,
    ScaleType = Enum.ScaleType.Slice,
    SliceCenter = Rect.new(24, 24, 276, 276),
    Size = UDim2.new(1, 40, 1, 40),
    Position = UDim2.new(0, -20, 0, -20),
    ZIndex = 0,
})
corner(glow, 20)

------------------------------------------------------------
-- TITLE BAR
------------------------------------------------------------
local TitleBar = new("Frame", {
    Name = "TitleBar",
    Parent = Main,
    BackgroundColor3 = Theme.Secondary,
    BorderSizePixel = 0,
    Size = UDim2.new(1, 0, 0, 42),
    Position = UDim2.new(0, 0, 0, 0),
    ZIndex = 3,
})
corner(TitleBar, 14)

local titleBarFix = new("Frame", {
    Parent = TitleBar,
    BackgroundColor3 = Theme.Secondary,
    BorderSizePixel = 0,
    Size = UDim2.new(1, 0, 0, 14),
    Position = UDim2.new(0, 0, 1, -14),
    ZIndex = 3,
})

local titleGrad = gradient(TitleBar, Theme.Secondary, Theme.Tertiary, 0)

local TitleLabel = new("TextLabel", {
    Parent = TitleBar,
    BackgroundTransparency = 1,
    Text = "  ⚡ LÉO'S CRAZY GUI",
    Font = Enum.Font.GothamBlack,
    TextSize = 15,
    TextColor3 = Theme.NeonPurple,
    TextXAlignment = Enum.TextXAlignment.Left,
    Size = UDim2.new(0.6, 0, 1, 0),
    Position = UDim2.new(0, 12, 0, 0),
    ZIndex = 4,
})

local Subtitle = new("TextLabel", {
    Parent = TitleBar,
    BackgroundTransparency = 1,
    Text = "v1.0 • Dandy's World Edition",
    Font = Enum.Font.Gotham,
    TextSize = 10,
    TextColor3 = Theme.TextDim,
    TextXAlignment = Enum.TextXAlignment.Left,
    Size = UDim2.new(0.6, 0, 1, 0),
    Position = UDim2.new(0, 155, 0, 2),
    ZIndex = 4,
})

-- Linha neon
local accentLine = new("Frame", {
    Parent = TitleBar,
    BackgroundColor3 = Theme.NeonPurple,
    BorderSizePixel = 0,
    Size = UDim2.new(1, 0, 0, 2),
    Position = UDim2.new(0, 0, 1, -2),
    ZIndex = 5,
})
gradient(accentLine, Theme.NeonPurple, Theme.AccentRed, 0)

-- Botões de controle
local function makeCtrlButton(text, posX, color, hoverColor)
    local btn = new("TextButton", {
        Parent = TitleBar,
        BackgroundColor3 = Theme.Tertiary,
        BorderSizePixel = 0,
        Text = text,
        Font = Enum.Font.GothamBold,
        TextSize = 14,
        TextColor3 = color,
        Size = UDim2.new(0, 28, 0, 24),
        Position = UDim2.new(1, posX, 0.5, -12),
        ZIndex = 5,
        AutoButtonColor = false,
    })
    corner(btn, 6)
    stroke(btn, color, 1, 0.5)
    btn.MouseEnter:Connect(function()
        tween(btn, 0.15, { BackgroundColor3 = hoverColor, TextColor3 = Color3.new(1,1,1) })
    end)
    btn.MouseLeave:Connect(function()
        tween(btn, 0.15, { BackgroundColor3 = Theme.Tertiary, TextColor3 = color })
    end)
    return btn
end

local CloseBtn      = makeCtrlButton("✕", -38, Theme.AccentRed, Theme.AccentRed)
local MinimizeBtn   = makeCtrlButton("—", -70, Theme.NeonPurple, Theme.NeonPurple)

------------------------------------------------------------
-- DRAG LOGIC
------------------------------------------------------------
do
    local dragging, dragInput, dragStart, startPos
    TitleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = Main.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    TitleBar.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            Main.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
end

------------------------------------------------------------
-- SIDEBAR (ABAS)
------------------------------------------------------------
local Sidebar = new("Frame", {
    Name = "Sidebar",
    Parent = Main,
    BackgroundColor3 = Theme.Secondary,
    BorderSizePixel = 0,
    Size = UDim2.new(0, 130, 1, -42),
    Position = UDim2.new(0, 0, 0, 42),
    ZIndex = 2,
})
padding(Sidebar, 8)

local SidebarList = new("UIListLayout", {
    Parent = Sidebar,
    Padding = UDim.new(0, 6),
    SortOrder = Enum.SortOrder.LayoutOrder,
})

local sidebarGrad = gradient(Sidebar, Theme.Secondary, Color3.fromRGB(14, 8, 22), 90)

local TabButtons = {}
local TabContents = {}

local function createTab(name, icon)
    local btn = new("TextButton", {
        Parent = Sidebar,
        BackgroundColor3 = Theme.Tertiary,
        BorderSizePixel = 0,
        Text = "  " .. icon .. "  " .. name,
        Font = Enum.Font.GothamBold,
        TextSize = 13,
        TextColor3 = Theme.TextDim,
        TextXAlignment = Enum.TextXAlignment.Left,
        Size = UDim2.new(1, 0, 0, 38),
        AutoButtonColor = false,
        ZIndex = 3,
    })
    corner(btn, 8)
    local s = stroke(btn, Theme.Stroke, 1, 0.4)

    btn.MouseEnter:Connect(function()
        if State.ActiveTab ~= name then
            tween(btn, 0.15, { BackgroundColor3 = Color3.fromRGB(36, 28, 48) })
        end
    end)
    btn.MouseLeave:Connect(function()
        if State.ActiveTab ~= name then
            tween(btn, 0.15, { BackgroundColor3 = Theme.Tertiary })
        end
    end)

    btn.MouseButton1Click:Connect(function()
        State.ActiveTab = name
        for n, t in pairs(TabButtons) do
            local active = (n == name)
            tween(t, 0.2, {
                BackgroundColor3 = active and Theme.NeonPurpleDark or Theme.Tertiary,
                TextColor3 = active and Color3.new(1,1,1) or Theme.TextDim,
            })
            TabContents[n].Visible = active
        end
    end)

    TabButtons[name] = btn
    return btn
end

local Content = new("Frame", {
    Name = "Content",
    Parent = Main,
    BackgroundColor3 = Theme.Background,
    BorderSizePixel = 0,
    Size = UDim2.new(1, -130, 1, -42),
    Position = UDim2.new(0, 130, 0, 42),
    ClipsDescendants = true,
    ZIndex = 2,
})
padding(Content, 12)

local function createContent(name)
    local f = new("ScrollingFrame", {
        Parent = Content,
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 1, 0),
        CanvasSize = UDim2.new(0, 0, 0, 0),
        ScrollBarThickness = 4,
        ScrollBarImageColor3 = Theme.NeonPurple,
        BorderSizePixel = 0,
        Visible = false,
        ZIndex = 3,
    })
    new("UIListLayout", {
        Parent = f,
        Padding = UDim.new(0, 8),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })
    f.AutomaticCanvasSize = Enum.AutomaticSize.Y
    TabContents[name] = f
    return f
end

------------------------------------------------------------
-- WIDGETS
------------------------------------------------------------
local function makeSection(parent, title, order)
    local holder = new("Frame", {
        Parent = parent,
        BackgroundColor3 = Theme.Secondary,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 0),
        AutomaticSize = Enum.AutomaticSize.Y,
        LayoutOrder = order or 0,
        ZIndex = 3,
    })
    corner(holder, 10)
    stroke(holder, Theme.NeonPurpleDark, 1, 0.5)
    padding(holder, 10)

    local layout = new("UIListLayout", {
        Parent = holder,
        Padding = UDim.new(0, 8),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    local header = new("TextLabel", {
        Parent = holder,
        BackgroundTransparency = 1,
        Text = title,
        Font = Enum.Font.GothamBold,
        TextSize = 12,
        TextColor3 = Theme.NeonPurple,
        TextXAlignment = Enum.TextXAlignment.Left,
        Size = UDim2.new(1, 0, 0, 18),
        LayoutOrder = 0,
        ZIndex = 4,
    })

    return holder
end

local function makeToggle(parent, label, default, callback, order)
    local row = new("Frame", {
        Parent = parent,
        BackgroundColor3 = Theme.Tertiary,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 34),
        LayoutOrder = order or 0,
        ZIndex = 4,
    })
    corner(row, 8)
    stroke(row, Theme.Stroke, 1, 0.6)

    local lbl = new("TextLabel", {
        Parent = row,
        BackgroundTransparency = 1,
        Text = label,
        Font = Enum.Font.Gotham,
        TextSize = 12,
        TextColor3 = Theme.Text,
        TextXAlignment = Enum.TextXAlignment.Left,
        Size = UDim2.new(1, -70, 1, 0),
        Position = UDim2.new(0, 12, 0, 0),
        ZIndex = 5,
    })

    local track = new("TextButton", {
        Parent = row,
        BackgroundColor3 = Theme.Stroke,
        BorderSizePixel = 0,
        Text = "",
        Size = UDim2.new(0, 44, 0, 20),
        Position = UDim2.new(1, -54, 0.5, -10),
        AutoButtonColor = false,
        ZIndex = 5,
    })
    corner(track, 10)

    local knob = new("Frame", {
        Parent = track,
        BackgroundColor3 = Color3.fromRGB(220, 220, 230),
        BorderSizePixel = 0,
        Size = UDim2.new(0, 16, 0, 16),
        Position = UDim2.new(0, 2, 0.5, -8),
        ZIndex = 6,
    })
    corner(knob, 10)

    local isOn = default or false
    local function apply()
        if isOn then
            tween(track, 0.2, { BackgroundColor3 = Theme.NeonPurple })
            tween(knob, 0.2, { Position = UDim2.new(1, -18, 0.5, -8) })
            tween(lbl, 0.2, { TextColor3 = Theme.NeonPurple })
        else
            tween(track, 0.2, { BackgroundColor3 = Theme.Stroke })
            tween(knob, 0.2, { Position = UDim2.new(0, 2, 0.5, -8) })
            tween(lbl, 0.2, { TextColor3 = Theme.Text })
        end
    end
    apply()

    track.MouseButton1Click:Connect(function()
        isOn = not isOn
        apply()
        State.Toggles[label] = isOn
        if callback then pcall(callback, isOn) end
    end)

    State.Toggles[label] = isOn
    return row
end

local function makeSlider(parent, label, min, max, default, callback, order)
    local row = new("Frame", {
        Parent = parent,
        BackgroundColor3 = Theme.Tertiary,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 52),
        LayoutOrder = order or 0,
        ZIndex = 4,
    })
    corner(row, 8)
    stroke(row, Theme.Stroke, 1, 0.6)

    local lbl = new("TextLabel", {
        Parent = row,
        BackgroundTransparency = 1,
        Text = label,
        Font = Enum.Font.Gotham,
        TextSize = 12,
        TextColor3 = Theme.Text,
        TextXAlignment = Enum.TextXAlignment.Left,
        Size = UDim2.new(0.7, 0, 0, 20),
        Position = UDim2.new(0, 12, 0, 6),
        ZIndex = 5,
    })

    local valLbl = new("TextLabel", {
        Parent = row,
        BackgroundTransparency = 1,
        Text = tostring(default),
        Font = Enum.Font.GothamBold,
        TextSize = 12,
        TextColor3 = Theme.NeonPurple,
        TextXAlignment = Enum.TextXAlignment.Right,
        Size = UDim2.new(0.3, -12, 0, 20),
        Position = UDim2.new(0.7, 0, 0, 6),
        ZIndex = 5,
    })

    local bar = new("Frame", {
        Parent = row,
        BackgroundColor3 = Theme.Stroke,
        BorderSizePixel = 0,
        Size = UDim2.new(1, -24, 0, 8),
        Position = UDim2.new(0, 12, 0, 34),
        ZIndex = 5,
    })
    corner(bar, 4)

    local fill = new("Frame", {
        Parent = bar,
        BackgroundColor3 = Theme.NeonPurple,
        BorderSizePixel = 0,
        Size = UDim2.new((default - min) / (max - min), 0, 1, 0),
        ZIndex = 6,
    })
    corner(fill, 4)
    gradient(fill, Theme.NeonPurple, Theme.AccentRed, 0)

    local knob = new("Frame", {
        Parent = bar,
        BackgroundColor3 = Color3.new(1,1,1),
        BorderSizePixel = 0,
        Size = UDim2.new(0, 14, 0, 14),
        Position = UDim2.new((default - min) / (max - min), -7, 0.5, -7),
        ZIndex = 7,
    })
    corner(knob, 10)
    stroke(knob, Theme.NeonPurple, 1.5, 0)

    local dragging = false
    local function update(input)
        local mouseX = input.Position.X
        local barAbs = bar.AbsolutePosition.X
        local barW = bar.AbsoluteSize.X
        local alpha = math.clamp((mouseX - barAbs) / barW, 0, 1)
        local value = math.floor(min + (max - min) * alpha + 0.5)
        fill.Size = UDim2.new(alpha, 0, 1, 0)
        knob.Position = UDim2.new(alpha, -7, 0.5, -7)
        valLbl.Text = tostring(value)
        State.Sliders[label] = value
        if callback then pcall(callback, value) end
    end

    bar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            update(input)
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch) then
            update(input)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)

    State.Sliders[label] = default
    return row
end

local function makeButton(parent, label, callback, order, color)
    local c = color or Theme.NeonPurple
    local btn = new("TextButton", {
        Parent = parent,
        BackgroundColor3 = Theme.Tertiary,
        BorderSizePixel = 0,
        Text = label,
        Font = Enum.Font.GothamBold,
        TextSize = 12,
        TextColor3 = c,
        Size = UDim2.new(1, 0, 0, 34),
        LayoutOrder = order or 0,
        AutoButtonColor = false,
        ZIndex = 4,
    })
    corner(btn, 8)
    stroke(btn, c, 1, 0.4)

    btn.MouseEnter:Connect(function()
        tween(btn, 0.15, { BackgroundColor3 = c, TextColor3 = Color3.new(1,1,1) })
    end)
    btn.MouseLeave:Connect(function()
        tween(btn, 0.15, { BackgroundColor3 = Theme.Tertiary, TextColor3 = c })
    end)
    btn.MouseButton1Click:Connect(function()
        if callback then pcall(callback) end
    end)
    return btn
end

local function makeDropdown(parent, label, options, callback, order)
    local row = new("Frame", {
        Parent = parent,
        BackgroundColor3 = Theme.Tertiary,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 34),
        LayoutOrder = order or 0,
        ClipsDescendants = true,
        ZIndex = 4,
    })
    corner(row, 8)
    stroke(row, Theme.Stroke, 1, 0.6)

    local lbl = new("TextLabel", {
        Parent = row,
        BackgroundTransparency = 1,
        Text = label,
        Font = Enum.Font.Gotham,
        TextSize = 12,
        TextColor3 = Theme.Text,
        TextXAlignment = Enum.TextXAlignment.Left,
        Size = UDim2.new(0.5, 0, 1, 0),
        Position = UDim2.new(0, 12, 0, 0),
        ZIndex = 5,
    })

    local current = options[1]
    local selected = new("TextButton", {
        Parent = row,
        BackgroundColor3 = Theme.Secondary,
        BorderSizePixel = 0,
        Text = current .. "  ▾",
        Font = Enum.Font.Gotham,
        TextSize = 11,
        TextColor3 = Theme.NeonPurple,
        Size = UDim2.new(0.45, 0, 0, 24),
        Position = UDim2.new(0.53, 0, 0.5, -12),
        AutoButtonColor = false,
        ZIndex = 5,
    })
    corner(selected, 6)
    stroke(selected, Theme.NeonPurple, 1, 0.5)

    local list = new("Frame", {
        Parent = row,
        BackgroundColor3 = Theme.Secondary,
        BorderSizePixel = 0,
        Size = UDim2.new(0.45, 0, 0, 0),
        Position = UDim2.new(0.53, 0, 0, 34),
        Visible = false,
        ZIndex = 20,
    })
    corner(list, 6)
    stroke(list, Theme.NeonPurple, 1, 0.3)

    local listLayout = new("UIListLayout", {
        Parent = list,
        Padding = UDim.new(0, 2),
        SortOrder = Enum.SortOrder.LayoutOrder,
    })

    local expanded = false
    local function refresh()
        for _, child in ipairs(list:GetChildren()) do
            if child:IsA("TextButton") then child:Destroy() end
        end
        for i, opt in ipairs(options) do
            local ob = new("TextButton", {
                Parent = list,
                BackgroundColor3 = Theme.Tertiary,
                BorderSizePixel = 0,
                Text = opt,
                Font = Enum.Font.Gotham,
                TextSize = 11,
                TextColor3 = Theme.Text,
                Size = UDim2.new(1, 0, 0, 26),
                LayoutOrder = i,
                AutoButtonColor = false,
                ZIndex = 21,
            })
            ob.MouseEnter:Connect(function() tween(ob, 0.1, { BackgroundColor3 = Theme.NeonPurpleDark }) end)
            ob.MouseLeave:Connect(function() tween(ob, 0.1, { BackgroundColor3 = Theme.Tertiary }) end)
            ob.MouseButton1Click:Connect(function()
                current = opt
                selected.Text = opt .. "  ▾"
                list.Visible = false
                expanded = false
                row.Size = UDim2.new(1, 0, 0, 34)
                if callback then pcall(callback, opt) end
            end)
        end
    end
    refresh()

    selected.MouseButton1Click:Connect(function()
        expanded = not expanded
        if expanded then
            list.Visible = true
            local h = math.min(#options * 28 + 6, 160)
            row.Size = UDim2.new(1, 0, 0, 34 + h)
            list.Size = UDim2.new(0.45, 0, 0, h)
        else
            list.Visible = false
            row.Size = UDim2.new(1, 0, 0, 34)
        end
    end)

    return row
end

------------------------------------------------------------
-- ABAS
------------------------------------------------------------
createTab("Home", "🏠")
createTab("AutoFarm", "⚙")
createTab("ESP", "👁")
createTab("Player", "🧍")
createTab("Settings", "⚡")

createContent("Home")
createContent("AutoFarm")
createContent("ESP")
createContent("Player")
createContent("Settings")

-- Ativa Home
TabButtons["Home"].BackgroundColor3 = Theme.NeonPurpleDark
TabButtons["Home"].TextColor3 = Color3.new(1,1,1)
TabContents["Home"].Visible = true

------------------------------------------------------------
-- CONTEÚDO: HOME
------------------------------------------------------------
do
    local f = TabContents["Home"]

    local welcome = new("Frame", {
        Parent = f,
        BackgroundColor3 = Theme.Secondary,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 74),
        LayoutOrder = 0,
    })
    corner(welcome, 10)
    stroke(welcome, Theme.NeonPurple, 1, 0.3)
    local wg = gradient(welcome, Color3.fromRGB(30, 18, 46), Theme.Secondary, 45)

    new("TextLabel", {
        Parent = welcome,
        BackgroundTransparency = 1,
        Text = "⚡ BEM-VINDO, " .. string.upper(LocalPlayer.Name),
        Font = Enum.Font.GothamBlack,
        TextSize = 16,
        TextColor3 = Theme.NeonPurple,
        TextXAlignment = Enum.TextXAlignment.Left,
        Size = UDim2.new(1, -20, 0, 24),
        Position = UDim2.new(0, 12, 0, 10),
    })

    new("TextLabel", {
        Parent = welcome,
        BackgroundTransparency = 1,
        Text = "Dandy's World • Executor Edition",
        Font = Enum.Font.Gotham,
        TextSize = 11,
        TextColor3 = Theme.TextDim,
        TextXAlignment = Enum.TextXAlignment.Left,
        Size = UDim2.new(1, -20, 0, 18),
        Position = UDim2.new(0, 12, 0, 36),
    })

    local status = new("TextLabel", {
        Parent = welcome,
        BackgroundTransparency = 1,
        Text = "● ONLINE",
        Font = Enum.Font.GothamBold,
        TextSize = 11,
        TextColor3 = Theme.Success,
        TextXAlignment = Enum.TextXAlignment.Right,
        Size = UDim2.new(0, 100, 0, 18),
        Position = UDim2.new(1, -112, 0, 36),
    })

    local info = makeSection(f, "📊 Informações da Sessão", 1)
    makeSlider(info, "FPS Alvo", 30, 240, 60, function(v) end, 1)
    makeSlider(info, "Distância Máxima (studs)", 10, 500, 150, function(v) end, 2)

    local modules = makeSection(f, "🧩 Status dos Módulos", 2)
    local modList = {"AutoFarm", "ESP", "Speed", "Jump", "Aimbot"}
    for i, m in ipairs(modList) do
        local row = new("Frame", {
            Parent = modules,
            BackgroundColor3 = Theme.Tertiary,
            BorderSizePixel = 0,
            Size = UDim2.new(1, 0, 0, 28),
            LayoutOrder = i,
        })
        corner(row, 6)
        new("TextLabel", {
            Parent = row,
            BackgroundTransparency = 1,
            Text = m,
            Font = Enum.Font.Gotham,
            TextSize = 12,
            TextColor3 = Theme.Text,
            TextXAlignment = Enum.TextXAlignment.Left,
            Size = UDim2.new(0.6, 0, 1, 0),
            Position = UDim2.new(0, 12, 0, 0),
        })
        local st = new("TextLabel", {
            Parent = row,
            BackgroundTransparency = 1,
            Text = "OFF",
            Font = Enum.Font.GothamBold,
            TextSize = 11,
            TextColor3 = Theme.AccentRed,
            TextXAlignment = Enum.TextXAlignment.Right,
            Size = UDim2.new(0.4, -12, 1, 0),
            Position = UDim2.new(0.6, 0, 0, 0),
            Name = m .. "Status",
        })
        State["Status_" .. m] = st
    end
end

------------------------------------------------------------
-- CONTEÚDO: AUTOFARM
------------------------------------------------------------
do
    local f = TabContents["AutoFarm"]

    local s1 = makeSection(f, "🌾 AutoFarm Principal", 0)
    makeToggle(s1, "Ativar AutoFarm", false, function(v)
        State.Modules.AutoFarm = v
        if State["Status_AutoFarm"] then
            State["Status_AutoFarm"].Text = v and "ON" or "OFF"
            State["Status_AutoFarm"].TextColor3 = v and Theme.Success or Theme.AccentRed
        end
    end, 1)
    makeDropdown(s1, "Modo", {"Coleta", "Missões", "Híbrido"}, function(v) end, 2)
    makeSlider(s1, "Velocidade de Ação", 1, 10, 3, function(v) end, 3)
    makeSlider(s1, "Raio de Detecção", 5, 200, 60, function(v) end, 4)

    local s2 = makeSection(f, "🎯 Tarefas Automáticas", 1)
    makeToggle(s2, "Auto Coletar Itens", false, nil, 1)
    makeToggle(s2, "Auto Completar Missões", false, nil, 2)
    makeToggle(s2, "Auto Vender", false, nil, 3)
    makeToggle(s2, "Auto Recompensa", false, nil, 4)
    makeToggle(s2, "Ignorar Distância", false, nil, 5)

    local s3 = makeSection(f, "🛠 Controles", 2)
    makeButton(s3, "▶ Iniciar AutoFarm", function()
        State.Modules.AutoFarm = true
        print("[Léo's GUI] AutoFarm iniciado")
    end, 1, Theme.Success)
    makeButton(s3, "⏸ Pausar AutoFarm", function()
        print("[Léo's GUI] AutoFarm pausado")
    end, 2, Theme.Warning)
    makeButton(s3, "⏹ Parar AutoFarm", function()
        State.Modules.AutoFarm = false
        print("[Léo's GUI] AutoFarm parado")
    end, 3, Theme.AccentRed)
end

------------------------------------------------------------
-- CONTEÚDO: ESP
------------------------------------------------------------
do
    local f = TabContents["ESP"]

    local s1 = makeSection(f, "👁 ESP - Personagens", 0)
    makeToggle(s1, "Ativar ESP", false, function(v)
        State.Modules.ESP = v
        if State["Status_ESP"] then
            State["Status_ESP"].Text = v and "ON" or "OFF"
            State["Status_ESP"].TextColor3 = v and Theme.Success or Theme.AccentRed
        end
    end, 1)
    makeToggle(s1, "Mostrar Nome", true, nil, 2)
    makeToggle(s1, "Mostrar Distância", true, nil, 3)
    makeToggle(s1, "Mostrar Vida", false, nil, 4)
    makeToggle(s1, "Caixa Delimitadora", false, nil, 5)
    makeToggle(s1, "Através de Paredes", true, nil, 6)

    local s2 = makeSection(f, "🎨 Estilo", 1)
    makeDropdown(s2, "Cor da Caixa", {"Roxo Neon", "Vermelho", "Verde", "Ciano"}, function(v) end, 1)
    makeSlider(s2, "Espessura da Caixa", 1, 5, 2, function(v) end, 2)
    makeSlider(s2, "Transparência", 0, 100, 20, function(v) end, 3)

    local s3 = makeSection(f, "📦 Objetos", 2)
    makeToggle(s3, "ESP Itens", false, nil, 1)
    makeToggle(s3, "ESP Máquinas", false, nil, 2)
    makeToggle(s3, "ESP Saídas", false, nil, 3)
    makeToggle(s3, "ESP Dandy", false, nil, 4)
end

------------------------------------------------------------
-- CONTEÚDO: PLAYER
------------------------------------------------------------
do
    local f = TabContents["Player"]

    local s1 = makeSection(f, "🧍 Movimento", 0)
    makeToggle(s1, "Speed Hack", false, function(v)
        State.Modules.Speed = v
        if State["Status_Speed"] then
            State["Status_Speed"].Text = v and "ON" or "OFF"
            State["Status_Speed"].TextColor3 = v and Theme.Success or Theme.AccentRed
        end
    end, 1)
    makeSlider(s1, "Velocidade", 16, 200, 16, function(v)
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum and State.Modules.Speed then hum.WalkSpeed = v end
    end, 2)
    makeToggle(s1, "Jump Hack", false, function(v)
        State.Modules.Jump = v
        if State["Status_Jump"] then
            State["Status_Jump"].Text = v and "ON" or "OFF"
            State["Status_Jump"].TextColor3 = v and Theme.Success or Theme.AccentRed
        end
    end, 3)
    makeSlider(s1, "Força do Pulo", 50, 300, 50, function(v)
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum and State.Modules.Jump then hum.JumpPower = v end
    end, 4)
    makeToggle(s1, "Infinite Jump", false, nil, 5)

    local s2 = makeSection(f, "🛡 Combate", 1)
    makeToggle(s2, "Aimbot", false, nil, 1)
    makeDropdown(s2, "Parte Alvo", {"Head", "HumanoidRootPart", "Nearest"}, function(v) end, 2)
    makeSlider(s2, "FOV", 30, 360, 90, function(v) end, 3)

    local s3 = makeSection(f, "🧬 Aparência", 2)
    makeButton(s3, "Resetar Personagem", function()
        local char = LocalPlayer.Character
        if char then
            char:BreakJoints()
        end
    end, 1, Theme.Warning)
    makeButton(s3, "Ir para Spawn", function()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp.CFrame = CFrame.new(0, 50, 0)
        end
    end, 2)
end

------------------------------------------------------------
-- CONTEÚDO: SETTINGS
------------------------------------------------------------
do
    local f = TabContents["Settings"]

    local s1 = makeSection(f, "⚙ Interface", 0)
    makeToggle(s1, "Desativar Sombra Neon", false, function(v)
        glow.Visible = not v
    end, 1)
    makeSlider(s1, "Arredondamento da Janela", 0, 24, 14, function(v)
        local c = Main:FindFirstChildOfClass("UICorner")
        if c then c.CornerRadius = UDim.new(0, v) end
    end, 2)
    makeToggle(s1, "Modo Compacto", false, nil, 3)

    local s2 = makeSection(f, "🎨 Cores do Tema", 1)
    makeDropdown(s2, "Tema", {"Roxo Neon (Padrão)", "Vermelho Sangue", "Ciano Cyber"}, function(v)
        local newColor = Theme.NeonPurple
        if v == "Vermelho Sangue" then newColor = Theme.AccentRed
        elseif v == "Ciano Cyber" then newColor = Color3.fromRGB(0, 200, 255) end
        mainStroke.Color = newColor
        glow.ImageColor3 = newColor
        accentLine.BackgroundColor3 = newColor
        TitleLabel.TextColor3 = newColor
    end, 1)
    makeSlider(s2, "Intensidade do Brilho", 0, 100, 60, function(v)
        glow.ImageTransparency = 1 - (v / 100)
    end, 2)

    local s3 = makeSection(f, "🔧 Sistema", 2)
    makeButton(s3, "🔄 Recarregar GUI", function()
        ScreenGui:Destroy()
    end, 1, Theme.Warning)
    makeButton(s3, "❌ Fechar GUI", function()
        ScreenGui:Destroy()
    end, 2, Theme.AccentRed)
    makeButton(s3, "💾 Salvar Configurações", function()
        print("[Léo's GUI] Configurações salvas.")
    end, 3, Theme.Success)
end

------------------------------------------------------------
-- BOTÕES DO TITLE
------------------------------------------------------------
CloseBtn.MouseButton1Click:Connect(function()
    tween(Main, 0.2, { Size = UDim2.new(0, 0, 0, 0) })
    task.wait(0.2)
    ScreenGui:Destroy()
end)

MinimizeBtn.MouseButton1Click:Connect(function()
    State.Minimized = not State.Minimized
    if State.Minimized then
        Main:TweenSize(UDim2.new(0, 560, 0, 42), Enum.EasingDirection.Out, Enum.EasingStyle.Quad, 0.3, true)
    else
        Main:TweenSize(UDim2.new(0, 560, 0, 400), Enum.EasingDirection.Out, Enum.EasingStyle.Quad, 0.3, true)
    end
end)

------------------------------------------------------------
-- EFEITO DE PULSO DO TÍTULO
------------------------------------------------------------
task.spawn(function()
    while ScreenGui.Parent do
        if not State.Minimized then
            tween(TitleLabel, 2, { TextColor3 = Theme.AccentRed }, Enum.EasingStyle.Sine)
            task.wait(2)
            tween(TitleLabel, 2, { TextColor3 = Theme.NeonPurple }, Enum.EasingStyle.Sine)
            task.wait(2)
        else
            task.wait(1)
        end
    end
end)

------------------------------------------------------------
-- ANÚNCIO
------------------------------------------------------------
print([[
╔══════════════════════════════════════════╗
║     ⚡ LÉO'S CRAZY GUI — v1.0            ║
║     Dandy's World Executor Edition       ║
║     Status: ✅ Carregado com sucesso     ║
╚══════════════════════════════════════════╝
]])
