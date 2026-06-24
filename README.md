# OTClient Redemption

OTClient Redemption e o client usado para conectar ao servidor OpenTibia. Ele combina core C++20 com interface e modulos em Lua, permitindo customizacao de tela, login, assets, efeitos, protocolos e comportamento do jogo.

Neste workspace, ele esta preparado para conversar com o `login-server` local em `127.0.0.1:8088` e com o Canary como servidor de jogo.

## Estrutura Importante

| Caminho | Descricao |
| --- | --- |
| `init.lua` | Primeiro arquivo executado pelo client; define servicos, servidores e modulos iniciais. |
| `config.ini` | Configuracoes publicas de graficos, atlas e fontes. |
| `data/` | Assets, estilos, fontes e configuracoes base. |
| `modules/` | Interface e funcionalidades em Lua. |
| `mods/` | Modificacoes opcionais. |
| `src/` | Codigo-fonte C++. |
| `bin/` | Binarios e DLLs de distribuicao Windows, incluindo `client.exe`. |
| `CMakePresets.json` | Presets de build para Windows, Linux e macOS. |
| `vcpkg/` | Gerenciador de dependencias usado na compilacao. |

## Requisitos

Para executar no Windows:

- Usar `bin/client.exe` com os arquivos presentes em `bin/`.

Para compilar:

- CMake 3.24 ou superior.
- Ninja.
- Compilador C++20.
- vcpkg.
- Dependencias graficas compatíveis com a plataforma.

## Configuracao de Login

O arquivo `init.lua` possui a tabela `Servers_init`. A entrada local atual e:

```lua
["127.0.0.1"] = {
    port = 8088,
    protocol = 1511,
    httpLogin = true,
    useAuthenticator = false
}
```

Use essa configuracao quando o `login-server` estiver rodando localmente na porta `8088`.

Se quiser conectar direto em outro servidor, ajuste:

- Host da entrada, por exemplo `meuservidor.com`.
- `port`, conforme login HTTP ou login protocol.
- `protocol`, conforme versao suportada pelo servidor/client.
- `httpLogin`, `true` para login via `login-server`; `false` para login protocol tradicional.

## Como Iniciar

### Windows

Execute:

```powershell
cd bin
.\client.exe
```

### macOS/Linux apos compilar

Depois do build, execute o binario gerado dentro de `build/<preset>/` a partir da raiz do projeto ou copie os artefatos conforme o pacote de distribuicao da sua plataforma.

Exemplo:

```bash
./build/macos-release/otclient
```

O nome exato do binario pode variar conforme a plataforma e o preset usado.

## Compilacao

### macOS

```bash
export VCPKG_ROOT="$PWD/vcpkg"
./vcpkg/bootstrap-vcpkg.sh
cmake --preset macos-release
cmake --build --preset macos-release
```

### Linux

```bash
export VCPKG_ROOT="$PWD/vcpkg"
./vcpkg/bootstrap-vcpkg.sh
cmake --preset linux-release
cmake --build --preset linux-release
```

### Windows

Em um terminal com ambiente do Visual Studio:

```powershell
$env:VCPKG_ROOT="$PWD\vcpkg"
.\vcpkg\bootstrap-vcpkg.bat
cmake --preset windows-release
cmake --build --preset windows-release
```

## Testes

Os presets de debug habilitam testes:

```bash
cmake --preset macos-debug
cmake --build --preset macos-debug
ctest --preset macos-debug
```

No Linux:

```bash
cmake --preset linux-debug
cmake --build --preset linux-debug
ctest --preset linux-debug
```

## Assets do Client

O `init.lua` possui instalacao automatica de assets habilitada:

```lua
clientAssets = {
    enabled = true,
    repository = "dudantas/tibia-client",
    installSounds = true
}
```

Na primeira execucao, o client pode baixar ou preparar arquivos necessarios de assets conforme a configuracao do modulo.

## Integracao com o Projeto

Fluxo local recomendado:

1. Suba o banco e o `canary`.
2. Inicie o `login-server`.
3. Abra o OTClient.
4. Conecte usando `127.0.0.1` com login HTTP na porta `8088`.

## Solucao de Problemas

| Problema | Verificacao |
| --- | --- |
| Client nao abre | Confira dependencias graficas, arquivos em `data/`, `modules/` e permissao do binario. |
| Tela de login nao conecta | Verifique se `login-server` esta rodando e se `init.lua` aponta para a porta correta. |
| Autentica mas nao entra | Confira `SERVER_IP` e `SERVER_PORT` no `login-server` e se o Canary esta online. |
| Assets faltando | Confira a configuracao `clientAssets` e conexao com a internet, ou instale assets manualmente. |

## Licenca

Distribuido sob a licenca MIT. Consulte `LICENSE` para detalhes.
