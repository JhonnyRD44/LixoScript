local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

local targetLocked = false
local lockedTarget = nil
local MAX_ANGLE = 8
local MAX_DISTANCE = 100

local function getClosestTarget()
	local closest, minAngle = nil, MAX_ANGLE
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
			local head = player.Character.Head
			local dirToHead = (head.Position - Camera.CFrame.Position).Unit
			local angle = math.deg(math.acos(Camera.CFrame.LookVector:Dot(dirToHead)))
			local distance = (head.Position - Camera.CFrame.Position).Magnitude

			if angle < minAngle and distance <= MAX_DISTANCE then
				closest, minAngle = player, angle
			end
		end
	end
	return closest
end

RunService.RenderStepped:Connect(function()
	if not targetLocked then
		local target = getClosestTarget()
		if target and target.Character and target.Character:FindFirstChild("Head") then
			lockedTarget = target
			targetLocked = true
		end
	end

	if targetLocked and lockedTarget and lockedTarget.Character and lockedTarget.Character:FindFirstChild("Head") then
		local headPos = lockedTarget.Character.Head.Position
		local camPos = Camera.CFrame.Position
		Camera.CFrame = CFrame.new(camPos, headPos)
	end
end)
