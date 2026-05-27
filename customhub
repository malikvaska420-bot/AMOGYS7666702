-- Загружаем популярную библиотеку интерфейсов Rayfield
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- Создаем главное окно
local Window = Rayfield:CreateWindow({
   Name = "Sol's RNG Custom Hub 🎲",
   LoadingTitle = "Загрузка скрипта...",
   LoadingSubtitle = "by Death in Solitude",
   ConfigurationSaving = {
      Enabled = false -- Сохранение настроек отключено
   },
   Discord = { Enabled = false },
   KeySystem = false
})

local HttpService = game:GetService("HttpService")
local requestFunc = http_request or request or HttpPost or syn.request

-- Переменные (Всегда пустые при запуске)
getgenv().WebhookURL = ""
getgenv().DiscordID = ""
getgenv().MinRarity = 100000000
getgenv().AutoMerchant = false
getgenv().EggNotifications = false
getgenv().BiomeNotifications = false
getgenv().GlobalNotifications = false

getgenv().SelectedBiomes = {"Sandstorm", "Hell", "Starfall", "Corruption", "Null", "Dreamspace", "Glitched", "Cyberspace", "Singularity"}

-- === ФУНКЦИЯ ОТПРАВКИ ВЕБХУКА ===
local function sendWebhook(title, description, color, needsPing)
    -- Если ссылка пустая (ты её не ввел), скрипт просто ничего не сделает
    if not requestFunc or getgenv().WebhookURL == "" then return end
    
    local pingText = ""
    if needsPing and getgenv().DiscordID ~= "" then
        for id in string.gmatch(getgenv().DiscordID, "[^,]+") do
            local cleanId = string.gsub(id, "%s+", "") 
            if cleanId ~= "" then
                pingText = pingText .. "<@" .. cleanId .. "> "
            end
        end
    end
    
    local data = {
        ["username"] = "Sol's RNG Hub",
        ["content"] = pingText,
        ["embeds"] = {{
            ["title"] = title,
            ["description"] = description,
            ["type"] = "rich",
            ["color"] = tonumber(color)
        }}
    }

    requestFunc({
        Url = getgenv().WebhookURL,
        Method = "POST",
        Headers = {["Content-Type"] = "application/json"},
        Body = HttpService:JSONEncode(data)
    })
end

-- === ФИЛЬТР ТВОИХ АУР ===
local function checkAndSendAura(auraName, rarityString)
    local cleanRarity = string.gsub(tostring(rarityString), ",", "")
    local rarityNumber = tonumber(cleanRarity)

    if rarityNumber and rarityNumber >= getgenv().MinRarity then
        local title = "🌟 ВЫБИТА СВЕРХРЕДКАЯ АУРА!"
        local desc = "Тебе выпала аура: **" .. auraName .. "**\nРедкость: 1 из **" .. rarityString .. "**"
        sendWebhook(title, desc, 0xFFD700, true)
    end
end

-- === ОТСЛЕЖИВАНИЕ ЧАТА (ГЛОБАЛКИ) ===
local TextChatService = game:GetService("TextChatService")
TextChatService.MessageReceived:Connect(function(textChatMessage)
    if not getgenv().GlobalNotifications then return end
    local msg = textChatMessage.Text
    if string.find(string.lower(msg), "rolled") and string.find(string.lower(msg), "1,") then
        sendWebhook("🌐 Глобальная Аура!", "Сообщение: *" .. msg .. "*", 0x8A2BE2, false)
    end
end)

-- === ОТСЛЕЖИВАНИЕ БИОМОВ ===
task.spawn(function()
    local lastBiome = "Normal"
    
    local biomeData = {
        ["windy"] = {color = 0xADD8E6, rarity = "1 / 500"},
        ["snowy"] = {color = 0xFFFAFA, rarity = "1 / 600"},
        ["rainy"] = {color = 0x4682B4, rarity = "1 / 750"},
        ["sandstorm"] = {color = 0xF4A460, rarity = "1 / 3,000"},
        ["hell"] = {color = 0xFF0000, rarity = "1 / 6,666"},
        ["starfall"] = {color = 0x00008B, rarity = "1 / 7,500"},
        ["corruption"] = {color = 0x8A2BE2, rarity = "1 / 9,000"},
        ["null"] = {color = 0x111111, rarity = "1 / 10,100"},
        ["dreamspace"] = {color = 0xFF69B4, rarity = "1 / 3,500,000"},
        ["glitched"] = {color = 0x800080, rarity = "1 / 30,000"},
        ["cyberspace"] = {color = 0x00FF00, rarity = "1 / 5,000"},
        ["singularity"] = {color = 0x000000, rarity = "1% шанс от Starfall"}
    }

    while task.wait(3) do
        if getgenv().BiomeNotifications then
            local currentBiome = "Normal"
            local biomeVal = game:GetService("ReplicatedStorage"):FindFirstChild("Biome") 
                             or game:GetService("Lighting"):FindFirstChild("Biome")
            
            if biomeVal and biomeVal:IsA("StringValue") then
                currentBiome = biomeVal.Value
            end

            if currentBiome ~= lastBiome and currentBiome ~= "Normal" then
                lastBiome = currentBiome
                local lowerBiome = string.lower(currentBiome)
                
                local isSelected = false
                for _, selected in pairs(getgenv().SelectedBiomes) do
                    if string.find(lowerBiome, string.lower(selected)) then
                        isSelected = true
                        break
                    end
                end

                if isSelected then
                    for bName, bInfo in pairs(biomeData) do
                        if string.find(lowerBiome, bName) then
                            local desc = "На сервере начался биом: **" .. currentBiome .. "**\nШанс появления: **" .. bInfo.rarity .. "**"
                            sendWebhook("🌌 Смена Биома!", desc, bInfo.color, true)
                            break
                        end
                    end
                end
            elseif currentBiome == "Normal" then
                lastBiome = "Normal"
            end
        end
    end
end)

-- === СОЗДАЕМ ВКЛАДКИ ===
local TabMain = Window:CreateTab("🏠 Главная", 4483362458)
local TabMap = Window:CreateTab("🌍 Биомы и Глобалки", 4483362458)
local TabWebhooks = Window:CreateTab("🌐 Вебхуки", 4483362458)

-- === ВКЛАДКА: ГЛАВНАЯ ===
TabMain:CreateSection("Уведомления о карте")

TabMain:CreateToggle({
   Name = "Отслеживать Мерчантов (Mari/Jester/Rin)",
   CurrentValue = false,
   Flag = "MerchantToggle", 
   Callback = function(Value) getgenv().AutoMerchant = Value end,
})

TabMain:CreateToggle({
   Name = "Отслеживать появление Яиц",
   CurrentValue = false,
   Flag = "EggToggle", 
   Callback = function(Value) getgenv().EggNotifications = Value end,
})

game:GetService("Workspace").DescendantAdded:Connect(function(child)
    if getgenv().AutoMerchant then
        if child.Name == "Mari" or child.Name == "Jester" or child.Name == "Rin" then
            task.wait(1)
            sendWebhook("🛒 Торговец прибыл!", "Мерчант **" .. child.Name .. "** появился на карте!", 0x00FFFF, true)
        end
    end
    if getgenv().EggNotifications then
        if string.find(string.lower(child.Name), "egg") then
            task.wait(0.5)
            sendWebhook("🥚 Найдено новое яйцо!", "На карте заспавнилось: **" .. child.Name .. "**", 0xFF69B4, false)
        end
    end
end)

-- === ВКЛАДКА: БИОМЫ И ГЛОБАЛКИ ===
TabMap:CreateSection("Настройки Биомов")

TabMap:CreateToggle({
   Name = "Включить уведомления о Биомах",
   CurrentValue = false,
   Flag = "BiomeToggle", 
   Callback = function(Value) getgenv().BiomeNotifications = Value end,
})

TabMap:CreateDropdown({
   Name = "Выбери нужные биомы",
   Options = {"Windy", "Snowy", "Rainy", "Sandstorm", "Hell", "Starfall", "Corruption", "Null", "Dreamspace", "Glitched", "Cyberspace", "Singularity"},
   CurrentOption = getgenv().SelectedBiomes,
   MultipleOptions = true,
   Flag = "BiomeDropdown",
   Callback = function(Options)
        getgenv().SelectedBiomes = Options
   end,
})

TabMap:CreateSection("Глобальные события сервера")

TabMap:CreateToggle({
   Name = "Уведомления о Глобальных Аурах в чате",
   CurrentValue = false,
   Flag = "GlobalToggle", 
   Callback = function(Value) getgenv().GlobalNotifications = Value end,
})

TabMap:CreateButton({
   Name = "Тест: Смена биома (Dreamspace)",
   Callback = function()
        sendWebhook("🌌 Смена Биома!", "На сервере начался биом: **Dreamspace**\nШанс появления: **1 / 3,500,000**", 0xFF69B4, true)
   end,
})

-- === ВКЛАДКА: ВЕБХУКИ (НАСТРОЙКА) ===
TabWebhooks:CreateSection("Настройки Discord")

TabWebhooks:CreateInput({
   Name = "Ссылка на Webhook",
   PlaceholderText = "Вставь ссылку сюда...",
   RemoveTextAfterFocusLost = false,
   Callback = function(Text) getgenv().WebhookURL = Text end,
})

TabWebhooks:CreateInput({
   Name = "Твои Discord ID (можно через запятую)",
   PlaceholderText = "Вставь свои ID сюда...",
   RemoveTextAfterFocusLost = false,
   Callback = function(Text) getgenv().DiscordID = Text end,
})

TabWebhooks:CreateInput({
   Name = "Ограничение редкости (твои ауры)",
   PlaceholderText = "Например: 100000000",
   RemoveTextAfterFocusLost = false,
   Callback = function(Text)
        local num = tonumber(Text)
        if num then getgenv().MinRarity = num else Rayfield:Notify({Title = "Ошибка", Content = "Только цифры!", Duration = 3}) end
   end,
})
