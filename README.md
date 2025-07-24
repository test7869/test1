local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

local function tweenTo(pos)
local distance = (hrp.Position - pos).Magnitude
local speed = 400  
local time = math.max(distance / speed, 0.01)
local tween = TweenService:Create(hrp, TweenInfo.new(time, Enum.EasingStyle.Linear), {
CFrame = CFrame.new(pos)
})
tween:Play()
tween.Completed:Wait()
end

local function getDeliveryPrompt()
local folder = workspace:FindFirstChild("DeliveryFolder")
if not folder then return nil end

local prompts = folder:FindFirstChild("Prompts")  
if not prompts then return nil end  

local prompt = prompts:FindFirstChild("Prompt1")  
if prompt and prompt:FindFirstChild("DeliveryPrompt") then  
    return prompt  
end  
return nil

end

local function waitForPrompt(timeout)
local start = tick()
while tick() - start < timeout do
local prompt = getDeliveryPrompt()
if prompt then
return prompt
end
task.wait(0.05)
end
return nil
end

local function firePromptRepeatedly(prompt, times, delay)
for i = 1, times do
pcall(function()
fireproximityprompt(prompt)
end)
task.wait(delay)
end
end

while true do

local promptPart = waitForPrompt(10)
if not promptPart then
warn("prompt não apareceu no tempo")
task.wait(1)
continue
end


local direction = (hrp.Position - promptPart.Position).Unit  
local targetPos = promptPart.Position + direction * -2 + Vector3.new(0, 2, 0)  
tweenTo(targetPos)  


hrp.Velocity = Vector3.zero  
task.wait(0.02)  


firePromptRepeatedly(promptPart.DeliveryPrompt, 5, 0.05)  


local beam = nil  
for i = 1, 100 do  
    beam = char:FindFirstChildWhichIsA("Beam", true)  
    if beam and beam.Attachment1 and beam.Attachment1.Parent:IsA("BasePart") then  
        break  
    end  
    task.wait(0.05)  
end  

if not beam or not beam.Attachment1 then  
    warn("beam não detectado, tentando de novo")  
    task.wait(1)  
    continue  
end  


local destino = beam.Attachment1.Parent  
tweenTo(destino.Position + Vector3.new(0, 3, 0))  
task.wait(0.2)

end
