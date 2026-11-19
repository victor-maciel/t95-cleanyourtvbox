# T95: limpar e proteger a sua TV box com um assistente de IA

Prompts em português para quem tem uma **TV box T95 / T95 Max / T95 Max Plus** (ou outro clone Amlogic genérico)
e quer:

1. **[Conter o malware de fábrica](01-prompt-malware.md)**: muitas dessas boxes vêm com apps escondidos que usam a
   sua internet como proxy e instalam coisas sem avisar. O prompt encontra, desativa e remove o que dá, e fecha o
   acesso pela rede.
2. **[Limpar apps, consertar o botão de desligar e trocar a tela inicial](02-prompt-limpeza-e-tela-inicial.md)**:
   remove os apps que você escolher e os instaladores `.apk` esquecidos, faz o botão de desligar do controle
   desligar de verdade e instala uma tela inicial no estilo Apple TV com recomendações de filmes e séries.

Os dois começam explicando como **liberar o modo desenvolvedor e a depuração** na box. Depois é só colar o prompt
num assistente com acesso ao terminal (Claude Code, Codex, Gemini CLI...) num computador na mesma rede.

## Como foi testado

Numa **T95 Max Plus** (Amlogic S905X3, placa `franklin`, Android 9, firmware `S905X3-20260509`,
build `userdebug/test-keys`). Nela foram encontrados pacotes com poder de sistema que agiam como proxy e loja
escondida, um app falso com nome do Google e restos de um ataque pela porta ADB aberta. Outras T95 podem ter outros
nomes de pacote: os prompts mandam o assistente investigar a sua box, não copiar uma lista.

## Avisos

- **Nada aqui usa root nem regrava firmware.** O malware é desativado, não apagado do sistema. A solução definitiva
  é trocar a box por um aparelho certificado pelo Google ou regravar um firmware limpo.
- **Tudo que é desativado pode ser reativado**: o assistente guarda um backup e explica como desfazer.
- **Desinstalar apps e apagar `.apk` não tem volta**: o assistente pergunta antes.
- **Feche o ADB no final.** Nessas boxes ele costuma aceitar conexão sem senha.

## Créditos

- Ferramentas e regras de segurança: [CleanYourTV](https://github.com/Gathin23/cleanyourtv) (MIT).
- Tela inicial com recomendações: [CleanYourTV UI](https://github.com/matheus-caldeira/cleanyourtv-ui), de
  matheus-caldeira, com dados do [TMDB](https://www.themoviedb.org). Este projeto não é endossado pelo TMDB.
