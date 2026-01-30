# Sistema de Carregamento e Menu

Um framework robusto para Roblox que substitui a experiência inicial padrão por um sistema de menu cinematográfico e uma tela de carregamento funcional que realiza o pré-carregamento real de ativos (assets) do jogo.

## Funcionalidades

- **Carregamento Real de Assets:** Utiliza `ContentProvider:PreloadAsync` para baixar malhas (meshes), texturas, decalques, sons e elementos de interface antes da entrada no jogo.
- **Câmera Orbital Cinemática:** Uma câmera que orbita o mapa em visão aérea durante o menu, com sistema de mola (Spring) para suavizar movimentos e efeito de mergulho (dive) ao iniciar o jogo.
- **Navegador de Jogadores:** Sistema de busca que localiza usuários pelo nick, exibe o avatar em tempo real e permite teleporte direto para a sessão do amigo (se ele estiver no mesmo jogo).
- **Interface Minimalista:** UI procedural (gerada via script) com efeitos de vidro, desfoque dinâmico e suporte total à língua portuguesa.
- **Performance:** Carregamento em lotes (batching) para evitar o congelamento do motor durante o download dos arquivos.

## Estrutura do Projeto

O projeto é dividido em dois componentes principais para garantir a segurança e o funcionamento das APIs de busca:

### 1. Servidor (ServerScriptService)
Gerencia as requisições de teleporte e consulta a API do Roblox para verificar o status dos jogadores.

### 2. Cliente (StarterPlayerScripts)
Controla toda a parte visual, animações de câmera, áudio e a lógica da interface do usuário.

## Instalação

### Configuração do Servidor
1. Crie um `Script` dentro de `ServerScriptService`.
2. Nomeie como `ServerHandler`.
3. Cole o código responsável por gerenciar as funções `GetFriendStatus` e o evento `TeleportToFriend`.

### Configuração do Cliente
1. Crie um `LocalScript` dentro de `StarterPlayerScripts`.
2. Nomeie como `ClientMenu`.
3. Cole o código principal que contém o framework da interface e o gerenciador de câmera.

### Comunicação (RemoteObjects)
Os objetos de comunicação (`RemoteFunction` e `RemoteEvent`) são criados automaticamente pelo script do servidor no `ReplicatedStorage`.

## Configurações Técnicas

Você pode ajustar o comportamento do sistema editando a tabela `Config` no topo do script de cliente:

```lua
local Config = {
    Assets = {
        Music = "rbxassetid://ID_DA_MUSICA",
        Click = "rbxassetid://ID_SOM_CLICK",
        Hover = "rbxassetid://ID_SOM_HOVER"
    },
    Colors = {
        Void = Color3.fromRGB(5, 5, 8),
        Primary = Color3.fromRGB(0, 255, 170), -- Cor dos detalhes (Neon)
        Text = Color3.fromRGB(255, 255, 255)
    },
    Camera = {
        Height = 300, -- Altura do drone no mapa
        Radius = 200, -- Raio da órbita
        Speed = 0.05  -- Velocidade da rotação
    }
}
