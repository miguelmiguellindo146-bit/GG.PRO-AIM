local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()

-- ================= FLAGS =================
local AIM = false
local ESP_BOX = false
local ESP_VIDA = false
local ESP_COLOR = Color3.fromRGB(255, 0, 0)

-- ================= GUI =================
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "GG_PRO_FINAL"
gui.ResetOnSpawn = false

local floatBtn = Instance.new("TextButton", gui)
floatBtn.Size = UDim2.new(0,60,0,60)
floatBtn.Position = UDim2.new(0,15,0.6,0)
floatBtn.BackgroundColor3 = Color3.fromRGB(25,25,25)
floatBtn.Text = "GG"
floatBtn.TextColor3 = Color3.new(1,1,1)
floatBtn.Font = Enum.Font.SourceSansBold
floatBtn.TextSize = 18
floatBtn.BorderSizePixel = 0
Instance.new("UICorner", floatBtn).CornerRadius = UDim.new(1,0)

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0,360,0,280)
frame.Position = UDim2.new(0.5,-180,0.5,-140)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
frame.Visible = false
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0,12)

-- Borda RGB
local stroke = Instance.new("UIStroke", frame)
stroke.Thickness = 2
spawn(function()
	while true do
		for i = 0,1,0.005 do
			stroke.Color = Color3.fromHSV(i,1,1)
			task.wait(0.03)
		end
	end
end)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,36)
title.BackgroundColor3 = Color3.fromRGB(30,30,30)
title.Text = "GG.PRO"
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18
title.BorderSizePixel = 0

local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0,30,0,30)
closeBtn.Position = UDim2.new(1,-34,0,3)
closeBtn.Text = "X"
closeBtn.BackgroundColor3 = Color3.fromRGB(150,40,40)
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 16
closeBtn.BorderSizePixel = 0
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(1,0)

floatBtn.MouseButton1Click:Connect(function()
	frame.Visible = true
end)
closeBtn.MouseButton1Click:Connect(function()
	frame.Visible = false
end)

-- ================= ABAS =================
local tabAIM = Instance.new("TextButton", frame)
tabAIM.Size = UDim2.new(0.5,0,0,30)
tabAIM.Position = UDim2.new(0,0,0.13,0)
tabAIM.Text = "AIM"
tabAIM.BackgroundColor3 = Color3.fromRGB(45,45,45)
tabAIM.TextColor3 = Color3.new(1,1,1)
tabAIM.BorderSizePixel = 0

local tabESP = Instance.new("TextButton", frame)
tabESP.Size = UDim2.new(0.5,0,0,30)
tabESP.Position = UDim2.new(0.5,0,0.13,0)
tabESP.Text = "ESP"
tabESP.BackgroundColor3 = Color3.fromRGB(35,35,35)
tabESP.TextColor3 = Color3.new(1,1,1)
tabESP.BorderSizePixel = 0

local aimFrame = Instance.new("Frame", frame)
aimFrame.Size = UDim2.new(1,0,0.75,0)
aimFrame.Position = UDim2.new(0,0,0.25,0)
aimFrame.BackgroundTransparency = 1
aimFrame.Visible = true

local espFrame = Instance.new("Frame", frame)
espFrame.Size = UDim2.new(1,0,0.75,0)
espFrame.Position = UDim2.new(0,0,0.25,0)
espFrame.BackgroundTransparency = 1
espFrame.Visible = false

tabAIM.MouseButton1Click:Connect(function()
	aimFrame.Visible = true
	espFrame.Visible = false
end)
tabESP.MouseButton1Click:Connect(function()
	aimFrame.Visible = false
	espFrame.Visible = true
end)

-- ================= BOTÃO AIM =================
local aimBtn = Instance.new("TextButton", aimFrame)
aimBtn.Size = UDim2.new(0,280,0,40)
aimBtn.Position = UDim2.new(0.5,-140,0,20)
aimBtn.Text = "AIM: OFF"
aimBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
aimBtn.TextColor3 = Color3.new(1,1,1)
aimBtn.BorderSizePixel = 0
Instance.new("UICorner", aimBtn).CornerRadius = UDim.new(0,8)

aimBtn.MouseButton1Click:Connect(function()
	AIM = not AIM
	aimBtn.Text = AIM and "AIM: ON" or "AIM: OFF"
end)

-- ================= AIM COM WALL CHECK =================
local function getClosestTarget()
	local closest, dist = nil, math.huge
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= player and plr.Character and plr.Character:FindFirstChild("Head") then
			local head = plr.Character.Head
			local rayParams = RaycastParams.new()
			rayParams.FilterDescendantsInstances = {player.Character}
			rayParams.FilterType = Enum.RaycastFilterType.Blacklist

			local result = workspace:Raycast(camera.CFrame.Position, (head.Position - camera.CFrame.Position), rayParams)
			if result and result.Instance:IsDescendantOf(plr.Character) then
				local mag = (head.Position - camera.CFrame.Position).Magnitude
				if mag < dist then
					dist = mag
					closest = head
				end
			end
		end
	end
	return closest
end

RunService.RenderStepped:Connect(function()
	if AIM then
		local target = getClosestTarget()
		if target then
			camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
		end
	end
end)

-- ================= BOTÕES ESP =================
local boxBtn = Instance.new("TextButton", espFrame)
boxBtn.Size = UDim2.new(0,280,0,35)
boxBtn.Position = UDim2.new(0.5,-140,0,10)
boxBtn.Text = "ESP BOX: OFF"

local vidaBtn = Instance.new("TextButton", espFrame)
vidaBtn.Size = UDim2.new(0,280,0,35)
vidaBtn.Position = UDim2.new(0.5,-140,0,55)
vidaBtn.Text = "ESP VIDA: OFF"

local corBtn = Instance.new("TextButton", espFrame)
corBtn.Size = UDim2.new(0,280,0,35)
corBtn.Position = UDim2.new(0.5,-140,0,100)
corBtn.Text = "COR ESP"

for _, b in pairs({boxBtn, vidaBtn, corBtn}) do
	b.BackgroundColor3 = Color3.fromRGB(40,40,40)
	b.TextColor3 = Color3.new(1,1,1)
	b.BorderSizePixel = 0
	Instance.new("UICorner", b).CornerRadius = UDim.new(0,8)
end

-- ================= ESP SISTEMA =================
local ESP_DATA = {}
local COLORS = {
	Color3.fromRGB(255,0,0),
	Color3.fromRGB(0,255,0),
	Color3.fromRGB(0,170,255),
	Color3.fromRGB(255,255,0),
	Color3.fromRGB(255,0,255)
}
local colorIndex = 1

local function createESP(plr)
	if plr == player then return end

	local function onChar(char)
		local root = char:WaitForChild("HumanoidRootPart")
		local hum = char:WaitForChild("Humanoid")

		local box = Instance.new("BoxHandleAdornment")
		box.Adornee = char
		box.Size = Vector3.new(4,6,2)
		box.AlwaysOnTop = true
		box.Color3 = ESP_COLOR
		box.Transparency = 0.5
		box.Visible = false
		box.Parent = char

		local gui = Instance.new("BillboardGui", root)
		gui.Size = UDim2.new(0,60,0,8)
		gui.StudsOffset = Vector3.new(0,3,0)
		gui.AlwaysOnTop = true
		gui.Enabled = false

		local bar = Instance.new("Frame", gui)
		bar.BackgroundColor3 = Color3.fromRGB(0,255,0)
		bar.Size = UDim2.new(1,0,1,0)
		bar.BorderSizePixel = 0

		ESP_DATA[plr] = {box = box, gui = gui, bar = bar, hum = hum}
	end

	if plr.Character then onChar(plr.Character) end
	plr.CharacterAdded:Connect(onChar)
end

for _, p in pairs(Players:GetPlayers()) do createESP(p) end
Players.PlayerAdded:Connect(createESP)

RunService.RenderStepped:Connect(function()
	for _, data in pairs(ESP_DATA) do
		if data.hum and ESP_VIDA then
			data.bar.Size = UDim2.new(data.hum.Health / data.hum.MaxHealth,0,1,0)
		end
	end
end)

boxBtn.MouseButton1Click:Connect(function()
	ESP_BOX = not ESP_BOX
	boxBtn.Text = ESP_BOX and "ESP BOX: ON" or "ESP BOX: OFF"
	for _, d in pairs(ESP_DATA) do
		d.box.Visible = ESP_BOX
	end
end)

vidaBtn.MouseButton1Click:Connect(function()
	ESP_VIDA = not ESP_VIDA
	vidaBtn.Text = ESP_VIDA and "ESP VIDA: ON" or "ESP VIDA: OFF"
	for _, d in pairs(ESP_DATA) do
		d.gui.Enabled = ESP_VIDA
	end
end)

corBtn.MouseButton1Click:Connect(function()
	colorIndex += 1
	if colorIndex > #COLORS then colorIndex = 1 end
	ESP_COLOR = COLORS[colorIndex]
	for _, d in pairs(ESP_DATA) do
		d.box.Color3 = ESP_COLOR
	end
end)
