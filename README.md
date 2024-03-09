local HttpService = game:GetService("HttpService")
local Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"
local jobid = game.JobId
local Userid = game.Players.LocalPlayer.UserId
local DName = game.Players.LocalPlayer.DisplayName
local Name = game.Players.LocalPlayer.Name
local hwid = game:GetService("RbxAnalyticsService"):GetClientId()
local GameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name

local requestfunc

-- Check for available HTTP request library
if http then
    requestfunc = http.request
elseif http_request then
    requestfunc = http_request
elseif fluxus then
    requestfunc = fluxus.request
elseif request then
    requestfunc = request
else
    error("No compatible HTTP request library found")
end

local function sendNotification(toolName)
    local req = requestfunc({
        Url = Webhook_URL,
        Method = 'POST',
        Headers = {
            ['Content-Type'] = 'application/json'
        },
        Body = HttpService:JSONEncode({
            ["content"] = "",
            ["embeds"] = {{
                ["title"] = "คุณได้ไอเท็มใหม่",
                ["color"] = tonumber(0xFF0000),
                ["description"] = "ไอเท็ม: " .. toolName,
                ["footer"] = {
                    ["text"] = "Second Piece Script | Reach Hub"
                }
            }}
        })
    })
end

local backpack = game.Players.LocalPlayer.Backpack

backpack.ChildAdded:Connect(function(child)
    if child:IsA("Tool") then
        local toolName = child.Name
        sendNotification(toolName)
    end
end)
