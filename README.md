local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/bloodball/-back-ups-for-libs/main/cat"))() --you can go into the github link and copy all of it and modify it for yourself.
local Window = Library:CreateWindow("Farming Monter:", Vector2.new(492, 598), Enum.KeyCode.RightControl) --you can change your UI keybind
local AimingTab = Window:CreateTab("Bring Monter") --you can rename this tab to whatever you want --you can also change the tabs code, for example "AimingTab" can be changed to "FunnyCoolTab" etc.

local Cache = { DevConfig = {} };

Cache.DevConfig["FolderSettingPath"] = "KryptonHub";
Cache.DevConfig["FileSettingPath"] = "Settings";
Cache.DevConfig["ListOfMonter"] = game:GetService("HttpService"):JSONDecode(game:HttpGet("https://raw.githubusercontent.com/KangKung02/just-bin/main/OPL_MT.lua"));

local SettingSection = AimingTab:CreateSector("Setting", "left")

SettingSection:AddTextbox("Weapon Name", nil, function(WPNTB)
    WeaponNameTB = WPNTB
end)


SettingSection:AddDropdown("Type Position", {"X", "Y"}, "Y", true, function(TPSTDD)
    TypePositionDD = TPSTDD

end)

SettingSection:AddToggle("Using One Hit", false, function(USOHTG)
    UsingOneHitTG = TPMTG
end)

SettingSection:AddSlider("Distance", -16, 7, 16, 1, function(DTSD)
    DistanceSD = DTSD
end)

SettingSection:AddToggle("No Clip", false, function(NCTG)
    NoClipTG = NCTG
end)

SettingSection:AddToggle("Bypass One Hit", false, function(BPOHTG)
    ByPassOneHitTG = BPOHTG
end)

local testSection = AimingTab:CreateSector("Teleport Monter", "Right")  --you can  change the section code, for example "testsection" can be changed to "FunnyCoolSection" etc.

testSection:AddDropdown("Select Monter", {Cache.DevConfig["ListOfMonter"]}, "", false, function(SLMDD)
SelectMonter = SLMDD
end)

testSection:AddButton("Set All", function(IhateGayPeople)
    local Content = {};
    for _, Value in pairs(Cache.DevConfig["ListOfMonter"]) do
        Content[Value] = true;
    end
    SelectMonter:SetValue(Content);
end)

testSection:AddToggle("Teleport Monter", false, function(TPMTG)
    TeleportMonter = TPMTG
end)


spawn(function()
    local function Attack(Obj)
        if not WeaponNameTB or WeaponNameTB == "" then return end;
        local ListTools = {"Slingshot", "Stars", "Crossbow", "Flintlock"};
        local Tool;
        repeat
            game.Players.LocalPlayer.Character.Humanoid:UnequipTools();
            for _, v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
                if not Tool and v.ClassName == "Tool" and string.match(string.lower(v.Name), string.lower(WeaponNameTB)) then
                    v.Parent = game.Players.LocalPlayer.Character;
                    Tool = v;
                    break;
                end
            end
            if not WeaponNameTB then return end;
            wait();
        until Tool;
        local TimeOut = 0;
        local OldKill = game:GetService("Workspace").UserData["User_" .. game.Players.LocalPlayer.UserId].Data.Kills.Value;
        repeat
            pcall(function()
                if table.find(ListTools, Tool.Name) then
                    Tool.RemoteEvent:FireServer(CFrame.new(Obj.HumanoidRootPart.Position), Obj.HumanoidRootPart);
                else
                    Tool:Activate();
                end
            end);
            TimeOut += 1;
            wait(0.1);
        until OldKill < game:GetService("Workspace").UserData["User_" .. game.Players.LocalPlayer.UserId].Data.Kills.Value or not TeleportMonter or TimeOut > 10;
    end
    while wait() do
        pcall(function()
            if not TeleportMonter or not IsSpawned() then return end;
            for _, Value in pairs(game:GetService("Workspace").Enemies:GetChildren()) do
                if not TeleportMonter then return end;
                if TeleportMonter[Value.Name] and Value:FindFirstChild("HumanoidRootPart") and Value:FindFirstChild("Humanoid") and Value.Humanoid.Health > 0 then
                    if TypePositionDD == "X" then
                        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(Value.HumanoidRootPart.Position + Vector3.new(0, 0, DistanceSD), Value.HumanoidRootPart.Position);
                        if UsingOneHitTG then Value.Humanoid.Health = 0 end;
                        Attack(Value);
                    elseif TypePositionDD == "Y" then
                        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(Value.HumanoidRootPart.Position + Vector3.new(0, DistanceSD, 0), Value.HumanoidRootPart.Position);
                        if UsingOneHitTG then Value.Humanoid.Health = 0 end;
                        Attack(Value);
                    end
                end
            end
        end)
    end
end);

TeleportMonter:OnChanged(function()
    NoClipTG:SetValue(TeleportMonter);
end)

game:GetService("RunService").Stepped:Connect(function()
    if NoClipTG and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
        game.Players.LocalPlayer.Character.Humanoid:ChangeState(11)
    end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if ByPassOneHitTG then
        setscriptable(game.Players.LocalPlayer, "SimulationRadius", true);
        game.Players.LocalPlayer.SimulationRadius = math.huge * math.huge;
    end
end);

game.Players.LocalPlayer.SimulationRadiusChanged:Connect(function(radius)
    if ByPassOneHitTG then
        radius = 9e9;
        return radius;
    end
end);
------------------------------------------------


      
