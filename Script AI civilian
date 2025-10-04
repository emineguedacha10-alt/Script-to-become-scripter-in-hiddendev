-- Services
local PathService = game:GetService("PathfindingService")
local ChatService = game:GetService("Chat")
local TweenService = game:GetService("TweenService")

-- NPC and Humanoid
local Civilian = workspace.Civilian
local Hum = Civilian.Humanoid

-- Main animations
local AnimTrack = Hum.Animator:LoadAnimation(script.Animation)
local AnimTrackSleep = Hum.Animator:LoadAnimation(script.SleepAnimation)
AnimTrackSleep.Looped = true

-- Random emotes
local randomEmote1 = Hum.Animator:LoadAnimation(script.a)
local randomEmote2 = Hum.Animator:LoadAnimation(script.b)
local randomEmote3 = Hum.Animator:LoadAnimation(script.c)
local anims = {randomEmote1, randomEmote2, randomEmote3}

-- Waypoints
local Wayparts = workspace.Wayparts:GetChildren()

-- NPC state
local IsIdle = true

-- Random chat phrases
local phrases = {
	"Hmm... where should I go next?",
	"What a calm day.",
	"I feel like walking more!",
	"Maybe I should take a rest soon...",
	"Walking around is fun!",
	"I love this place!",
	"Maybe I should sit for a while."
}

-- Function to turn the NPC toward a player
function Turn(player)
	local Tween = TweenService:Create(
		Civilian.HumanoidRootPart,
		TweenInfo.new(0.3),
		{CFrame = CFrame.new(Civilian.HumanoidRootPart.Position, player.Character.HumanoidRootPart.Position)}
	)
	Tween:Play()
end

-- Create path with forbidden zones
local NewPath = PathService:CreatePath({
	["Costs"] = {["ForbiddenZone"] = math.huge}
})

-- Main movement function
function GotoAPath()
	if IsIdle == false then return end

	-- Calculate a random target position
	local targetPos = Wayparts[math.random(1,#Wayparts)].Position + Vector3.new(math.random(1,5),0,math.random(1,5))
	NewPath:ComputeAsync(Civilian.HumanoidRootPart.Position, targetPos)
	IsIdle = false

	-- Move through all waypoints
	local Waypoints = NewPath:GetWaypoints()
	for i, waypoint in pairs(Waypoints) do
		Hum:MoveTo(waypoint.Position)
		Hum.MoveToFinished:Wait()
	end

	-- NPC looks around after moving
	for i = 1, 4 do
		Civilian.HumanoidRootPart.CFrame = Civilian.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(90), 0)
		task.wait(0.4)
	end

	-- Play a random animation
	if math.random(1, 4) == 2 then
		local anim = anims[math.random(1,#anims)]
		if not anim.IsPlaying then
			anim:Play()
			task.wait(2)
			anim:Stop()
		end
	end

	-- Speak randomly
	if math.random(1,5) == 3 then
		ChatService:Chat(Civilian.Head, phrases[math.random(1,#phrases)], Enum.ChatColor.White)
	end

	-- Interact with Seat or Bed in workspace
	if math.random(1,3) == 1 then
		IsIdle = false
		local randomnumber = math.random(1,2)
		for i,v in pairs(workspace:GetDescendants()) do
			if v.Name == "Seat" and randomnumber == 1 then
				NewPath:ComputeAsync(Civilian.HumanoidRootPart.Position, v.Position)
				local Waypoints = NewPath:GetWaypoints()
				for i,waypoint in pairs(Waypoints) do
					Hum:MoveTo(waypoint.Position)
					task.wait(0.2)
				end
				v:Sit(Hum) -- Sit the civilian
				task.wait(2)
				ChatService:Chat(Civilian.Head,"What a beautiful day... 😊",Enum.ChatColor.Green)
				task.wait(4)
				Hum.Jump = true
				IsIdle = true
				break
			elseif v.Name == "Bed" and randomnumber == 2 then
				NewPath:ComputeAsync(Civilian.HumanoidRootPart.Position, v.Position)
				local Waypoints = NewPath:GetWaypoints()
				for i,waypoint in pairs(Waypoints) do
					Hum:MoveTo(waypoint.Position)
					task.wait(0.2)
				end
				v:Sit(Hum)
				AnimTrackSleep:Play() -- Play sleep animation
				task.wait(2)
				game.SoundService["Dog Sleeping Meme "]:Play() -- Play Sleeping sound
				ChatService:Chat(Civilian.Head,"GoodNight ...",Enum.ChatColor.White)
				task.wait(4)
				game.SoundService["Dog Sleeping Meme "]:Stop()
				AnimTrackSleep:Stop()
				Hum.Jump = true
				IsIdle = true
				break
			end
		end
	end

	IsIdle = true
end

-- Player detection using Raycast
task.spawn(function()
	local cooldown = false
	while task.wait() and cooldown == false do
		local Rayc = workspace:Raycast(Civilian.Head.Position, Civilian.Head.CFrame.LookVector*50)
		if Rayc ~= nil then
			for i, player in pairs(game.Players:GetChildren()) do
				if player.Name == Rayc.Instance.Parent.Parent.Name and IsIdle == true then
					AnimTrack:Play()
					cooldown = true
					task.spawn(function()
						if IsIdle == true then
							task.wait(1)
							Turn(player)
						end
					end)
					ChatService:Chat(Civilian.Head,"Hello ".. player.Name,Enum.ChatColor.White)
					task.wait(9)
					cooldown = false
				end
			end
		end
	end
end)

-- Continuous movement loop
while true do
	task.wait(8)
	task.spawn(function()
		GotoAPath()
	end)
end
