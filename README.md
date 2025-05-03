-- Configurações básicas
local aimFov = 30            -- Campo de visão do aimbot
local aimKey = "mouse1"      -- Tecla de ativação (ex: botão do mouse)
local targetBone = "head"    -- Parte do corpo para mirar
local teamCheckEnabled = true

-- Função para checar se duas entidades são do mesmo time
function isSameTeam(localPlayer, targetPlayer)
    return localPlayer.team == targetPlayer.team
end

-- Calcula distância angular entre a mira e o jogador alvo
function getAimDistance(playerPos, targetPos)
    return (playerPos - targetPos).magnitude
end

-- Busca o inimigo mais próximo dentro do FOV
function getClosestTarget(localPlayer, players)
    local bestTarget = nil
    local shortestDistance = aimFov

    for _, player in pairs(players) do
        if player ~= localPlayer and player.alive then
            if not teamCheckEnabled or not isSameTeam(localPlayer, player) then
                local distance = getAimDistance(localPlayer.aimPosition, player.bonePositions[targetBone])
                if distance < shortestDistance then
                    shortestDistance = distance
                    bestTarget = player
                end
            end
        end
    end

    return bestTarget
end

-- Aimbot loop
function aimbotLoop(localPlayer, players)
    if isKeyDown(aimKey) then
        local target = getClosestTarget(localPlayer, players)
        if target then
            localPlayer:aimAt(target.bonePositions[targetBone])
        end
    end
end

-- Simulando loop do jogo
function onGameTick()
    localPlayer = getLocalPlayer()
    players = getAllPlayers()

    aimbotLoop(localPlayer, players)
end

-- Função de bind fictício para rodar a cada frame
setTickFunction(onGameTick)
