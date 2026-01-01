-- [[ GANTZ SYSTEM: TRACKING BULLET ]]
-- แรงบันดาลใจจากอนิเมะ Gantz: กระสุนล็อคเป้าอัตโนมัติ

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- ฟังก์ชันหาเป้าหมายที่ใกล้ที่สุด (เหมือนระบบล็อคเป้าใน Gantz)
function GetClosestPlayer()
    local Target = nil
    local Distance = math.huge
    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            local ScreenPos, OnScreen = game.Workspace.CurrentCamera:WorldToViewportPoint(v.Character.HumanoidRootPart.Position)
            if OnScreen then
                local Mag = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(ScreenPos.X, ScreenPos.Y)).Magnitude
                if Mag < Distance then
                    Distance = Mag
                    Target = v
                end
            end
        end
    end
    return Target
end

-- ระบบ Silent Aim (กระสุนติดตาม 100%)
local mt = getrawmetatable(game)
setreadonly(mt, false)
local oldNamecall = mt.__namecall

mt.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    local args = {...}
    if method == "FindPartOnRayWithIgnoreList" and not checkcaller() then
        local T = GetClosestPlayer()
        if T and T.Character and T.Character:FindFirstChild("HumanoidRootPart") then
            args[1] = Ray.new(game.Workspace.CurrentCamera.CFrame.Position, (T.Character.HumanoidRootPart.Position - game.Workspace.CurrentCamera.CFrame.Position).unit * 1000)
            return oldNamecall(self, unpack(args))
        end
    end
    return oldNamecall(self, ...)
end)
