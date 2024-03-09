HttpService = game:GetService("HttpService")
Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"

local function checkFrames()
  -- ตรวจสอบ WeaponFrame
  local weaponFrame = game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame
  if weaponFrame:IsDescendantOf(game.Players.LocalPlayer.PlayerGui) then
    local weaponName = weaponFrame.Name
    sendWebhook("WeaponFrame", weaponName)
  end

  -- ตรวจสอบ ItemsFrame
  local itemsFrame = game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame
  if itemsFrame:IsDescendantOf(game.Players.LocalPlayer.PlayerGui) then
    local itemName = itemsFrame.Name
    sendWebhook("ItemsFrame", itemName)
  end

  -- ตรวจสอบ Backpack
  local backpack = game.Players.LocalPlayer.Backpack
  for _, item in pairs(backpack:GetChildren()) do
    if item:IsA("Tool") then
      local toolName = item.Name
      sendWebhook("Backpack", toolName)
    end
  end
end

function sendWebhook(frameType, frameName)
  local jobid = game.JobId
  local Userid = game.Players.LocalPlayer.UserId
  local DName = game.Players.LocalPlayer.DisplayName
  local Name = game.Players.LocalPlayer.Name
  local hwid = game:GetService("RbxAnalyticsService"):GetClientId()
  local GameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name

  local requestfunc = http and http.request or http_request or fluxus and fluxus.request or request

  local req = requestfunc({
    Url = Webhook_URL,
    Method = 'POST',
    Headers = {
      ['Content-Type'] = 'application/json'
    },
    Body = HttpService:JSONEncode({
      ["content"] = "",
      ["embeds"] = {{
        ["title"] = "มี Frame หรือ Tool ใหม่เข้ามาใน Inventory",
        ["description"] = "Display Name: "..DName.." \nUsername: " .. Name.." \nUser Id: "..Userid.."\nHwid: "..hwid.."\nGame: "..GameName.."\nJob Id: "..jobid.."\n\nFrame Type: " .. frameType .. "\nFrame Name: " .. frameName
      }}
    })
  })
end

-- ตรวจสอบ Frame ใหม่ทุกๆ 0.5 วินาที
game:GetService("RunService").Heartbeat:Connect(function()
  checkFrames()
end)
