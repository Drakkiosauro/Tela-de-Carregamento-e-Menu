local Players = game:GetService("Players")
local ReplicatedFirst = game:GetService("ReplicatedFirst")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local ContentProvider = game:GetService("ContentProvider")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local SoundService = game:GetService("SoundService")
local StarterGui = game:GetService("StarterGui")
local Lighting = game:GetService("Lighting")
local Debris = game:GetService("Debris")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

UserInputService.MouseIconEnabled = true

local Config = {
	Assets = {
		Music = "rbxassetid://1848354536",
		Click = "rbxassetid://6042054045",
		Hover = "rbxassetid://6895079853"
	},
	Colors = {
		Void = Color3.fromRGB(5, 5, 8),
		Surface = Color3.fromRGB(15, 15, 20),
		SurfaceHigh = Color3.fromRGB(25, 25, 35),
		Primary = Color3.fromRGB(0, 255, 170),
		Text = Color3.fromRGB(255, 255, 255),
		TextDim = Color3.fromRGB(180, 180, 180),
		Error = Color3.fromRGB(255, 80, 80),
		Success = Color3.fromRGB(80, 255, 100)
	},
	Camera = {
		Height = 300,
		Radius = 200,
		Speed = 0.05
	}
}

ReplicatedFirst:RemoveDefaultLoadingScreen()
StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, false)

local Interface = {}
Interface.__index = Interface

function Interface.new()
	local self = setmetatable({}, Interface)
	self.Screen = Instance.new("ScreenGui")
	self.Screen.Name = "UltraMenu"
	self.Screen.IgnoreGuiInset = true
	self.Screen.ResetOnSpawn = false
	self.Screen.DisplayOrder = 1000
	self.Screen.Parent = PlayerGui
	return self
end

function Interface:CreateCanvas(name, z)
	local c = Instance.new("CanvasGroup")
	c.Name = name
	c.Size = UDim2.new(1, 0, 1, 0)
	c.BackgroundTransparency = 1
	c.ZIndex = z or 1
	c.Parent = self.Screen
	return c
end

function Interface:Blur(val)
	local b = Lighting:FindFirstChild("MenuBlur") or Instance.new("BlurEffect")
	b.Name = "MenuBlur"
	b.Parent = Lighting
	TweenService:Create(b, TweenInfo.new(0.5), {Size = val}):Play()
end

function Interface:CreateButton(parent, text, size, pos, callback)
	local btn = Instance.new("TextButton")
	btn.Size = size
	btn.Position = pos
	btn.BackgroundColor3 = Config.Colors.Surface
	btn.Text = ""
	btn.AutoButtonColor = false
	btn.Parent = parent

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 6)
	corner.Parent = btn

	local stroke = Instance.new("UIStroke")
	stroke.Color = Config.Colors.SurfaceHigh
	stroke.Thickness = 1
	stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
	stroke.Parent = btn

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -30, 1, 0)
	label.Position = UDim2.new(0, 30, 0, 0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.Font = Enum.Font.GothamBold
	label.TextSize = 16
	label.TextColor3 = Config.Colors.Text
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.ZIndex = 2
	label.Parent = btn

	local bar = Instance.new("Frame")
	bar.Size = UDim2.new(0, 3, 0.6, 0)
	bar.Position = UDim2.new(0, 0, 0.2, 0)
	bar.BackgroundColor3 = Config.Colors.Primary
	bar.BorderSizePixel = 0
	bar.BackgroundTransparency = 1
	bar.Parent = btn

	btn.MouseEnter:Connect(function()
		TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = Config.Colors.SurfaceHigh}):Play()
		TweenService:Create(stroke, TweenInfo.new(0.2), {Color = Config.Colors.Primary}):Play()
		TweenService:Create(bar, TweenInfo.new(0.2), {BackgroundTransparency = 0}):Play()
		TweenService:Create(label, TweenInfo.new(0.2), {Position = UDim2.new(0, 40, 0, 0)}):Play()

		local s = Instance.new("Sound")
		s.SoundId = Config.Assets.Hover
		s.Parent = SoundService
		s:Play()
		Debris:AddItem(s, 1)
	end)

	btn.MouseLeave:Connect(function()
		TweenService:Create(btn, TweenInfo.new(0.3), {BackgroundColor3 = Config.Colors.Surface}):Play()
		TweenService:Create(stroke, TweenInfo.new(0.3), {Color = Config.Colors.SurfaceHigh}):Play()
		TweenService:Create(bar, TweenInfo.new(0.3), {BackgroundTransparency = 1}):Play()
		TweenService:Create(label, TweenInfo.new(0.3), {Position = UDim2.new(0, 30, 0, 0)}):Play()
	end)

	btn.MouseButton1Click:Connect(function()
		local s = Instance.new("Sound")
		s.SoundId = Config.Assets.Click
		s.Parent = SoundService
		s:Play()
		Debris:AddItem(s, 1)
		if callback then callback() end
	end)

	return btn
end

local AssetManager = {}
AssetManager.Total = 0
AssetManager.Loaded = 0

function AssetManager:Start(onProgress)
	task.spawn(function()
		local list = {}
		for _, v in ipairs(workspace:GetDescendants()) do
			if v:IsA("MeshPart") or v:IsA("Texture") or v:IsA("Sound") or v:IsA("Decal") then
				table.insert(list, v)
			end
		end
		for _, v in ipairs(PlayerGui:GetDescendants()) do
			if v:IsA("ImageLabel") or v:IsA("ImageButton") then
				table.insert(list, v)
			end
		end

		self.Total = #list
		local batch = 5

		for i = 1, self.Total, batch do
			local chunk = {}
			for j=0, batch-1 do
				if list[i+j] then table.insert(chunk, list[i+j]) end
			end
			pcall(function() ContentProvider:PreloadAsync(chunk) end)

			self.Loaded = math.min(self.Loaded + batch, self.Total)

			local assetName = "Desconhecido"
			if list[i] then
				assetName = list[i].Name ~= "" and list[i].Name or list[i].ClassName
			end

			if onProgress then onProgress(self.Loaded / self.Total, assetName) end
			if i % 20 == 0 then RunService.RenderStepped:Wait() end
		end
		if onProgress then onProgress(1, "Concluído") end
	end)
end

local App = {}
App.UI = Interface.new()
App.Music = nil

function App:Init()
	local char = Player.Character or Player.CharacterAdded:Wait()
	local root = char:WaitForChild("HumanoidRootPart")
	local hum = char:WaitForChild("Humanoid")
	root.Anchored = true
	hum.WalkSpeed = 0

	self.Root = root
	self.Hum = hum

	self:SetupCamera()
	self:LoadingScreen()
end

function App:SetupCamera()
	Camera.CameraType = Enum.CameraType.Scriptable
	local angle = 0
	RunService:BindToRenderStep("MenuCam", Enum.RenderPriority.Camera.Value, function(dt)
		angle = angle + (dt * Config.Camera.Speed)
		local x = math.sin(angle) * Config.Camera.Radius
		local z = math.cos(angle) * Config.Camera.Radius
		Camera.CFrame = CFrame.new(Vector3.new(x, Config.Camera.Height, z), Vector3.new(0, 0, 0))
	end)
end

function App:LoadingScreen()
	local layer = self.UI:CreateCanvas("Loading", 100)

	local bg = Instance.new("Frame")
	bg.Size = UDim2.new(1, 0, 1, 0)
	bg.BackgroundColor3 = Config.Colors.Void
	bg.Parent = layer

	local title = Instance.new("TextLabel")
	title.Text = "CARREGANDO ASSETS"
	title.Font = Enum.Font.GothamBlack
	title.TextSize = 32
	title.TextColor3 = Config.Colors.Text
	title.Position = UDim2.new(0.5, 0, 0.45, 0)
	title.AnchorPoint = Vector2.new(0.5, 0.5)
	title.BackgroundTransparency = 1
	title.Parent = layer

	local bar = Instance.new("Frame")
	bar.Size = UDim2.new(0.3, 0, 0.005, 0)
	bar.Position = UDim2.new(0.35, 0, 0.55, 0)
	bar.BackgroundColor3 = Config.Colors.SurfaceHigh
	bar.BorderSizePixel = 0
	bar.Parent = layer

	local fill = Instance.new("Frame")
	fill.Size = UDim2.new(0, 0, 1, 0)
	fill.BackgroundColor3 = Config.Colors.Primary
	fill.BorderSizePixel = 0
	fill.Parent = bar

	local info = Instance.new("TextLabel")
	info.Text = "Iniciando..."
	info.Font = Enum.Font.Gotham
	info.TextSize = 12
	info.TextColor3 = Config.Colors.TextDim
	info.Position = UDim2.new(0, 0, 2, 0)
	info.Size = UDim2.new(1, 0, 5, 0)
	info.BackgroundTransparency = 1
	info.Parent = bar

	local skip = Instance.new("TextButton")
	skip.Text = "PULAR"
	skip.Font = Enum.Font.GothamBold
	skip.TextSize = 12
	skip.TextColor3 = Config.Colors.TextDim
	skip.Size = UDim2.new(0, 100, 0, 30)
	skip.Position = UDim2.new(0.5, -50, 0.85, 0)
	skip.BackgroundTransparency = 1
	skip.Parent = layer

	local done = false

	skip.MouseButton1Click:Connect(function()
		done = true
		self:ShowMenu(layer)
	end)

	AssetManager:Start(function(prog, name)
		if done then return end
		TweenService:Create(fill, TweenInfo.new(0.1), {Size = UDim2.new(prog, 0, 1, 0)}):Play()
		info.Text = "Baixando: " .. string.upper(name)

		if prog >= 1 then
			done = true
			self:ShowMenu(layer)
		end
	end)

	self.Music = Instance.new("Sound")
	self.Music.SoundId = Config.Assets.Music
	self.Music.Looped = true
	self.Music.Volume = 0
	self.Music.Parent = SoundService
	self.Music:Play()
	TweenService:Create(self.Music, TweenInfo.new(5), {Volume = 0.8}):Play()
end

function App:ShowMenu(loadingLayer)
	TweenService:Create(loadingLayer, TweenInfo.new(1), {GroupTransparency = 1}):Play()
	self.UI:Blur(20)
	task.wait(1)
	loadingLayer:Destroy()

	local menu = self.UI:CreateCanvas("MainMenu", 50)

	local side = Instance.new("Frame")
	side.Size = UDim2.new(0.25, 0, 1, 0)
	side.BackgroundColor3 = Config.Colors.Surface
	side.BackgroundTransparency = 0.05
	side.BorderSizePixel = 0
	side.Parent = menu

	local line = Instance.new("Frame")
	line.Size = UDim2.new(0, 1, 1, 0)
	line.Position = UDim2.new(1, 0, 0, 0)
	line.BackgroundColor3 = Config.Colors.SurfaceHigh
	line.BorderSizePixel = 0
	line.Parent = side

	local avatar = Instance.new("ImageLabel")
	avatar.Size = UDim2.new(0, 70, 0, 70)
	avatar.Position = UDim2.new(0, 30, 0, 50)
	avatar.BackgroundColor3 = Config.Colors.SurfaceHigh
	avatar.Image = Players:GetUserThumbnailAsync(Player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size150x150)
	avatar.Parent = side
	local corner = Instance.new("UICorner") corner.CornerRadius = UDim.new(1,0) corner.Parent = avatar
	local str = Instance.new("UIStroke") str.Color = Config.Colors.Primary str.Thickness = 2 str.Parent = avatar

	local name = Instance.new("TextLabel")
	name.Text = Player.DisplayName
	name.Font = Enum.Font.GothamBlack
	name.TextSize = 24
	name.TextColor3 = Config.Colors.Text
	name.Position = UDim2.new(0, 30, 0, 130)
	name.Size = UDim2.new(1, -60, 0, 30)
	name.TextXAlignment = Enum.TextXAlignment.Left
	name.BackgroundTransparency = 1
	name.Parent = side

	local container = Instance.new("Frame")
	container.Size = UDim2.new(1, -60, 0.5, 0)
	container.Position = UDim2.new(0, 30, 0.3, 0)
	container.BackgroundTransparency = 1
	container.Parent = side

	local layout = Instance.new("UIListLayout") layout.Padding = UDim.new(0, 15) layout.Parent = container

	self.UI:CreateButton(container, "JOGAR AGORA", UDim2.new(1, 0, 0, 50), UDim2.new(), function()
		self:PlayGame(menu)
	end)

	self.UI:CreateButton(container, "BUSCAR AMIGOS", UDim2.new(1, 0, 0, 50), UDim2.new(), function()
		self:ToggleServerBrowser(menu)
	end)

	local ping = Instance.new("TextLabel")
	ping.Position = UDim2.new(0, 30, 0.95, 0)
	ping.Size = UDim2.new(1, 0, 0, 20)
	ping.BackgroundTransparency = 1
	ping.TextColor3 = Config.Colors.TextDim
	ping.Font = Enum.Font.Code
	ping.TextSize = 14
	ping.TextXAlignment = Enum.TextXAlignment.Left
	ping.Parent = side

	task.spawn(function()
		while menu.Parent do
			ping.Text = "PING: " .. math.floor(Player:GetNetworkPing()*2000) .. "ms"
			task.wait(1)
		end
	end)
end

function App:ToggleServerBrowser(layer)
	local exists = layer:FindFirstChild("ServerBrowser")
	if exists then exists:Destroy() return end

	local frame = Instance.new("Frame")
	frame.Name = "ServerBrowser"
	frame.Size = UDim2.new(0.4, 0, 0.6, 0)
	frame.Position = UDim2.new(0.4, 0, 0.2, 0)
	frame.BackgroundColor3 = Config.Colors.Surface
	frame.BorderSizePixel = 0
	frame.Parent = layer

	local c = Instance.new("UICorner") c.CornerRadius = UDim.new(0, 10) c.Parent = frame
	local s = Instance.new("UIStroke") s.Color = Config.Colors.SurfaceHigh s.Thickness = 1 s.Parent = frame

	local title = Instance.new("TextLabel")
	title.Text = "ENCONTRAR JOGADOR"
	title.Font = Enum.Font.GothamBlack
	title.TextSize = 20
	title.TextColor3 = Config.Colors.Text
	title.Position = UDim2.new(0, 20, 0, 20)
	title.Size = UDim2.new(1, 0, 0, 30)
	title.BackgroundTransparency = 1
	title.TextXAlignment = Enum.TextXAlignment.Left
	title.Parent = frame

	local input = Instance.new("TextBox")
	input.Size = UDim2.new(1, -40, 0, 50)
	input.Position = UDim2.new(0, 20, 0, 60)
	input.BackgroundColor3 = Config.Colors.SurfaceHigh
	input.PlaceholderText = "Digite o nick..."
	input.Text = ""
	input.TextColor3 = Config.Colors.Text
	input.Font = Enum.Font.Gotham
	input.TextSize = 16
	input.TextXAlignment = Enum.TextXAlignment.Left
	input.Parent = frame

	local ic = Instance.new("UICorner") ic.CornerRadius = UDim.new(0, 6) ic.Parent = input
	local ip = Instance.new("UIPadding") ip.PaddingLeft = UDim.new(0, 15) ip.Parent = input

	local resultArea = Instance.new("Frame")
	resultArea.Size = UDim2.new(1, -40, 0.5, 0)
	resultArea.Position = UDim2.new(0, 20, 0, 120)
	resultArea.BackgroundTransparency = 1
	resultArea.Parent = frame

	input.FocusLost:Connect(function(enter)
		if enter and input.Text ~= "" then
			resultArea:ClearAllChildren()

			local statusTxt = Instance.new("TextLabel")
			statusTxt.Text = "Buscando..."
			statusTxt.TextColor3 = Config.Colors.TextDim
			statusTxt.Font = Enum.Font.Gotham
			statusTxt.Size = UDim2.new(1, 0, 0, 30)
			statusTxt.BackgroundTransparency = 1
			statusTxt.Parent = resultArea

			local rf = ReplicatedStorage:FindFirstChild("GetFriendStatus")
			if rf then
				local success, data = rf:InvokeServer(input.Text)
				statusTxt:Destroy()

				local card = Instance.new("Frame")
				card.Size = UDim2.new(1, 0, 0, 100)
				card.BackgroundColor3 = Config.Colors.SurfaceHigh
				card.Parent = resultArea
				local cc = Instance.new("UICorner") cc.CornerRadius = UDim.new(0, 8) cc.Parent = card

				local pic = Instance.new("ImageLabel")
				pic.Size = UDim2.new(0, 60, 0, 60)
				pic.Position = UDim2.new(0, 10, 0, 10)
				pic.BackgroundColor3 = Config.Colors.Void
				pic.Parent = card
				local pc = Instance.new("UICorner") pc.CornerRadius = UDim.new(0, 6) pc.Parent = pic

				local n = Instance.new("TextLabel")
				n.Text = input.Text
				n.Font = Enum.Font.GothamBold
				n.TextSize = 18
				n.TextColor3 = Config.Colors.Text
				n.Position = UDim2.new(0, 80, 0, 15)
				n.Size = UDim2.new(0, 200, 0, 20)
				n.BackgroundTransparency = 1
				n.TextXAlignment = Enum.TextXAlignment.Left
				n.Parent = card

				local st = Instance.new("TextLabel")
				st.Font = Enum.Font.Gotham
				st.TextSize = 14
				st.Position = UDim2.new(0, 80, 0, 40)
				st.Size = UDim2.new(0, 200, 0, 20)
				st.BackgroundTransparency = 1
				st.TextXAlignment = Enum.TextXAlignment.Left
				st.Parent = card

				if success and data.id then
					pic.Image = Players:GetUserThumbnailAsync(data.id, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100)
					if data.online then
						st.Text = "● ONLINE"
						st.TextColor3 = Config.Colors.Success

						local joinBtn = Instance.new("TextButton")
						joinBtn.Size = UDim2.new(0, 120, 0, 30)
						joinBtn.Position = UDim2.new(1, -130, 0.5, -15)
						joinBtn.BackgroundColor3 = Config.Colors.Success
						joinBtn.Text = "JUNTAR-SE"
						joinBtn.Font = Enum.Font.GothamBold
						joinBtn.TextColor3 = Color3.fromRGB(0,0,0)
						joinBtn.Parent = card
						local jc = Instance.new("UICorner") jc.CornerRadius = UDim.new(0, 4) jc.Parent = joinBtn

						joinBtn.MouseButton1Click:Connect(function()
							local teleportEvent = ReplicatedStorage:FindFirstChild("TeleportToFriend")
							if teleportEvent then
								statusTxt.Text = "Teleportando..."
								teleportEvent:FireServer(data.placeId, data.jobId)
							end
						end)
					else
						st.Text = "● OFFLINE"
						st.TextColor3 = Config.Colors.Error
					end
				else
					st.Text = "Não encontrado"
					st.TextColor3 = Config.Colors.Error
				end
			end
		end
	end)
end

function App:PlayGame(layer)
	TweenService:Create(layer, TweenInfo.new(0.5), {GroupTransparency = 1}):Play()
	TweenService:Create(self.Music, TweenInfo.new(1), {Volume = 0}):Play()
	self.UI:Blur(0)

	local targetCF = self.Root.CFrame * CFrame.new(0, 2, -10)
	local tween = TweenService:Create(Camera, TweenInfo.new(2, Enum.EasingStyle.Exponential), {CFrame = targetCF})
	tween:Play()
	tween.Completed:Wait()

	layer:Destroy()
	self.UI.Screen:Destroy()
	self.Music:Destroy()
	RunService:UnbindFromRenderStep("MenuCam")

	self.Root.Anchored = false
	self.Hum.WalkSpeed = 16
	Camera.CameraType = Enum.CameraType.Custom
	StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, true)
end

App:Init()



-- LEIA O README.md para entender como usar !!!!!
