local HttpService = game:GetService("HttpService")
local Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"
local jobid = game.JobId
local Userid = game.Players.LocalPlayer.UserId
local DName = game.Players.LocalPlayer.DisplayName
local Name = game.Players.LocalPlayer.Name
local GameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
local requestfunc = http and http.request or http_request or fluxus and fluxus.request or request

local Names = {
    ["WeaponFrame"] = {},
    ["ItemsFrame"] = {},
    ["Backpack"] = {},
}

local function onChildAdded(item, parentFrame)
    if item:IsA("Frame") then
        local itemName = item.Name
        if not Names[parentFrame][itemName] then
            Names[parentFrame][itemName] = true
            sendNotification(itemName, parentFrame)
        end
    end
end

local function sendNotification(itemName, parentFrame)
    local req = requestfunc({
        Url = Webhook_URL,
        Method = 'POST',
        Headers = {
            ['Content-Type'] = 'application/json'
        },
        Body = HttpService:JSONEncode({
            ["content"] = "",
            ["embeds"] = {{
                ["title"] = "ไอเท็มใหม่เพิ่มเข้ามา",
                ["color"] = tonumber(0xFF0000),
                ["description"] = "Username: " .. Name .. "\nGame: " .. GameName .. "\nItem Obtained: " .. itemName .. "\nLocation: " .. parentFrame,
                ["footer"] = {
                    ["text"] = "#1 Second Piece Script | Reach Hub"
                }
            }}
        })
    })
end

game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame.ChildAdded:Connect(function(item)
    onChildAdded(item, "WeaponFrame")
end)

game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame.ChildAdded:Connect(function(item)
    onChildAdded(item, "ItemsFrame")
end)

game.Players.LocalPlayer.Character.ChildAdded:Connect(function(item)
    onChildAdded(item, "Backpack")
end)

while true do
    wait()

    for _, item in ipairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame:GetChildren()) do
        if item:IsA("Frame") then
            onChildAdded(item, "WeaponFrame")
        end
    end

    for _, item in ipairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame:GetChildren()) do
        if item:IsA("Frame") then
            onChildAdded(item, "ItemsFrame")
        end
    end

    for _, item in ipairs(game.Players.LocalPlayer.Character:GetChildren()) do
        if item:IsA("Frame") then
            onChildAdded(item, "Backpack")
        end
    end
end
