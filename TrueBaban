local CollectionService = game:GetService("CollectionService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local localPlayer = Players.LocalPlayer
local hasKey = false -- Переменная, показывающая, подобран ли ключ

-- Функция для создания подсветки (ESP)
local function applyHighlight(instance, color)
    if not instance:FindFirstChildOfClass("Highlight") then
        local highlight = Instance.new("Highlight")
        highlight.FillColor = color
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 0
        highlight.Adornee = instance
        highlight.Parent = instance
    end
end

-- 1. ЛОГИКА ПОДСВЕТКИ (Подсвечиваем предметы сразу при их появлении)
local function setupESP()
    for _, key in ipairs(CollectionService:GetTagged("Key")) do
        applyHighlight(key, Color3.fromRGB(0, 0, 255)) -- Синий для ключей
    end
    for _, gold in ipairs(CollectionService:GetTagged("Gold")) do
        applyHighlight(gold, Color3.fromRGB(255, 215, 0)) -- Золотой для золота
    end
    for _, door in ipairs(CollectionService:GetTagged("Door")) do
        applyHighlight(door, Color3.fromRGB(255, 0, 0)) -- Красный для дверей
    end
end

-- 2. СБОР ПРЕДМЕТОВ И ВЗАИМОДЕЙСТВИЕ (Каждый кадр проверяем дистанцию)
RunService.Heartbeat:Connect(function()
    local character = localPlayer.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return end
    local hrp = character.HumanoidRootPart

    -- Сбор Ключей
    for _, key in ipairs(CollectionService:GetTagged("Key")) do
        if key:IsA("BasePart") and (key.Position - hrp.Position).Magnitude < 7 then
            hasKey = true
            print("[Delta]: Вы подобрали ключ!")
            key:Destroy() -- Удаляем ключ для себя (визуально подобрали)
        end
    end

    -- Сбор Золота
    for _, gold in ipairs(CollectionService:GetTagged("Gold")) do
        if gold:IsA("BasePart") and (gold.Position - hrp.Position).Magnitude < 7 then
            print("[Delta]: Золото собрано!")
            
            -- Клиентское обновление лидерборда (если он есть)
            local leaderstats = localPlayer:FindFirstChild("leaderstats")
            if leaderstats then
                local goldStat = leaderstats:FindFirstChild("Gold") or leaderstats:FindFirstChild("Золото")
                if goldStat then
                    goldStat.Value = goldStat.Value + 1
                end
            end
            gold:Destroy()
        end
    end

    -- Открытие Дверей
    for _, door in ipairs(CollectionService:GetTagged("Door")) do
        if door:IsA("BasePart") and (door.Position - hrp.Position).Magnitude < 8 then
            if hasKey then
                print("[Delta]: Дверь успешно открыта ключом!")
                hasKey = false -- Ключ потрачен
                
                -- Анимация открытия: делаем дверь невидимой и проходимой
                door.CanCollide = false
                door.Transparency = 1
                
                -- Если внутри двери есть замок (модель или парт), прячем и его
                for _, child in ipairs(door:GetChildren()) do
                    if child:IsA("BasePart") then
                        child.CanCollide = false
                        child.Transparency = 1
                    end
                end
            else
                -- Если ключа нет, дверь не пустит
                door.CanCollide = true
            end
        end
    end
end)

-- Инициализируем подсветку
setupESP()
-- Следим за динамически появляющимися объектами с тегами
CollectionService:GetInstanceAddedSignal("Key"):Connect(function(inst) applyHighlight(inst, Color3.fromRGB(0, 0, 255)) end)
CollectionService:GetInstanceAddedSignal("Gold"):Connect(function(inst) applyHighlight(inst, Color3.fromRGB(255, 215, 0)) end)
CollectionService:GetInstanceAddedSignal("Door"):Connect(function(inst) applyHighlight(inst, Color3.fromRGB(255, 0, 0)) end)

print("[Delta]: Скрипт на двери, золото и ключи успешно запущен!")
