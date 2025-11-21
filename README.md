# Animation
local TweenService = game:GetService("TweenService")
local gui = script.Parent

local frame1 = gui:FindFirstChild("Frame1")
local frame2 = gui:FindFirstChild("Frame2")

-- Labels
local creditsLabel = frame2:FindFirstChild("CreditsLabel")
local shopLabel = frame2:FindFirstChild("ShopLabel")
local updatesLabel = frame2:FindFirstChild("UpdatesLabel")

-- Botões
local creditsButton = frame1:FindFirstChild("CreditsButton")
local shopButton = frame1:FindFirstChild("ShopButton")
local updatesButton = frame1:FindFirstChild("UpdatesButton")
local playButton = frame1:FindFirstChild("PlayButton")

-- TextButtons do Frame1
local frame1Buttons = {
	CreditsButton = creditsButton,
	ShopButton = shopButton,
	UpdatesButton = updatesButton,
	PlayButton = playButton
}

-- Salva as posições originais dos labels
local shownPositions = {
	CreditsLabel = creditsLabel.Position,
	ShopLabel = shopLabel.Position,
	UpdatesLabel = updatesLabel.Position
}

-- Salva as posições originais dos botões do Frame1
local buttonShownPositions = {}
for name, btn in frame1Buttons do
	buttonShownPositions[name] = btn.Position
end

-- Calcula a posição escondida (fora da tela à direita) para cada label
local function getHiddenPos(label)
	return UDim2.new(1.2, 0, label.Position.Y.Scale, label.Position.Y.Offset)
end

-- Calcula a posição escondida à esquerda para cada botão do Frame1
local function getButtonHiddenLeftPos(btn)
	return UDim2.new(-0.4, 0, btn.Position.Y.Scale, btn.Position.Y.Offset)
end

-- Calcula a posição escondida à direita para cada botão do Frame1 (usado para voltar)
local function getButtonHiddenRightPos(btn)
	return UDim2.new(1.2, 0, btn.Position.Y.Scale, btn.Position.Y.Offset)
end

local labels = {
	CreditsLabel = creditsLabel,
	ShopLabel = shopLabel,
	UpdatesLabel = updatesLabel
}

-- Função para esconder todos os labels
local function hideAllLabels()
	for name, label in labels do
		TweenService:Create(label, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = getHiddenPos(label)}):Play()
	end
end

-- Função para mostrar um label específico
local function showLabel(label, labelName)
	TweenService:Create(label, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = shownPositions[labelName]}):Play()
end

-- Inicializa todos os labels escondidos
hideAllLabels()

-- Inicializa todos os botões do Frame1 escondidos à esquerda
for name, btn in frame1Buttons do
	btn.Position = getButtonHiddenLeftPos(btn)
	btn.Visible = true
end

-- Função para abrir um label e fechar os outros
local function openLabel(labelName)
	for name, label in labels do
		if name == labelName then
			showLabel(label, name)
		else
			TweenService:Create(label, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = getHiddenPos(label)}):Play()
		end
	end
end

-- Função para animar fade out de um frame e seus filhos
local function fadeOutFrame(frame)
	TweenService:Create(frame, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency = 1}):Play()
	for _, child in frame:GetChildren() do
		if child:IsA("TextLabel") or child:IsA("TextButton") then
			TweenService:Create(child, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {TextTransparency = 1, BackgroundTransparency = 1}):Play()
		end
	end
end

-- Função para esconder todos os botões do Frame1 para a esquerda
local function hideAllFrame1ButtonsLeft()
	for name, btn in frame1Buttons do
		TweenService:Create(btn, TweenInfo.new(0.8, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = getButtonHiddenLeftPos(btn)}):Play()
	end
end

-- Função para mostrar todos os botões do Frame1 vindo da direita (posição original)
local function showAllFrame1ButtonsRight()
	for name, btn in frame1Buttons do
		btn.Position = getButtonHiddenRightPos(btn)
		btn.Visible = true
		TweenService:Create(btn, TweenInfo.new(0.8, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = buttonShownPositions[name]}):Play()
	end
end

-- Botão Credits
creditsButton.MouseButton1Click:Connect(function()
	hideAllFrame1ButtonsLeft()
	openLabel("CreditsLabel")
end)

-- Botão Shop
shopButton.MouseButton1Click:Connect(function()
	hideAllFrame1ButtonsLeft()
	openLabel("ShopLabel")
end)

-- Botão Updates
updatesButton.MouseButton1Click:Connect(function()
	hideAllFrame1ButtonsLeft()
	openLabel("UpdatesLabel")
end)

-- Botão Play: fade out dos frames
if playButton then
	playButton.MouseButton1Click:Connect(function()
		fadeOutFrame(frame1)
		fadeOutFrame(frame2)
		task.wait(0.6)
		frame1.Visible = false
		frame2.Visible = false
	end)
end

-- Função para esconder label específico para a direita
local function hideLabelRight(label)
	TweenService:Create(label, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = getHiddenPos(label)}):Play()
end

-- Conectar todos os "Back" dos labels do Frame2
local function connectBackButtons()
	for name, label in labels do
		local back = label:FindFirstChild("Back")
		if back and back:IsA("TextButton") then
			back.MouseButton1Click:Connect(function()
				showAllFrame1ButtonsRight()
				hideLabelRight(label)
			end)
		end
	end
end

connectBackButtons()


