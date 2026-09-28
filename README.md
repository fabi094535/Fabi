-- Roube um Ovo - exemplo open source
-- Roblox / Luau

local Players = game:GetService("Players")

local egg = workspace:WaitForChild("Egg")
local deliveryZone = workspace:WaitForChild("DeliveryZone")

local carrying = {}

local function attachEgg(player)
	if carrying[player] then return end

	local character = player.Character
	if not character then return end

	local root = character:FindFirstChild("HumanoidRootPart")
	if not root then return end

	carrying[player] = egg

	egg.Anchored = false
	egg.CanCollide = false
	egg.CFrame = root.CFrame * CFrame.new(0, 2, -2)

	local weld = Instance.new("WeldConstraint")
	weld.Name = "EggWeld"
	weld.Part0 = egg
	weld.Part1 = root
	weld.Parent = egg
end

local function dropEgg(player)
	if not carrying[player] then return end

	local weld = egg:FindFirstChild("EggWeld")
	if weld then
		weld:Destroy()
	end

	egg.CanCollide = true
	egg.Anchored = true
	carrying[player] = nil
end

egg.Touched:Connect(function(hit)
	local character = hit.Parent
	local player = Players:GetPlayerFromCharacter(character)

	if player then
		attachEgg(player)
	end
end)

deliveryZone.Touched:Connect(function(hit)
	local character = hit.Parent
	local player = Players:GetPlayerFromCharacter(character)

	if player and carrying[player] then
		dropEgg(player)

		print(player.Name .. " entregou o ovo!")

		-- Exemplo de recompensa:
		local leaderstats = player:FindFirstChild("leaderstats")
		if leaderstats then
			local coins = leaderstats:FindFirstChild("Coins")
			if coins then
				coins.Value += 100
			end
		end
	end
end)

Players.PlayerRemoving:Connect(function(player)
	carrying[player] = nil
end)
