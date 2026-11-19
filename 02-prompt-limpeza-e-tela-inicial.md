# Prompt 2: limpar apps, consertar o botão de desligar e instalar uma tela inicial nova na T95

Para a **TV box T95 / T95 Max / T95 Max Plus** e clones Amlogic com Android 9 ou mais novo.
**Faça o [Prompt 1](01-prompt-malware.md) antes** se ainda não fez: não vale a pena arrumar a aparência de uma box
com malware ativo.

Este prompt:

- lista os apps instalados e remove os que **você** escolher, além dos instaladores `.apk` esquecidos na box;
- faz o **botão de desligar do controle desligar de verdade**, em vez de só pôr a box em descanso;
- instala uma tela inicial nova no estilo Apple TV: o **CleanYourTV UI**, com recomendações de filmes e séries do
  TMDB em português e "Assistir em" que abre o título direto no app de streaming.

Você vai precisar de:

- um computador na **mesma rede** que a box;
- um assistente com acesso ao terminal (**Claude Code**, Codex, Gemini CLI ou parecido);
- para a tela inicial com recomendações: uma **chave grátis da API do TMDB**, que você mesmo cria (veja abaixo).

---

## Passo 0: liberar o modo desenvolvedor e a depuração (na box, com o controle)

1. Abra **Configurações** → **Sobre o dispositivo** (em algumas boxes: Configurações → Sistema → Sobre).
2. Desça até **"Número da versão"** (às vezes "Build" ou "Versão do build").
3. Selecione e aperte **OK 7 vezes seguidas**, até aparecer "Você agora é um desenvolvedor".
4. Volte e abra **Opções do desenvolvedor** (normalmente em Configurações → Sistema, às vezes em
   Sistema → Avançado).
5. Ligue **Depuração USB**. Se existir, ligue também **Depuração de rede / ADB pela rede**.
6. Anote o **IP da box**: Configurações → Rede (ou Sobre o dispositivo → Endereço IP).

**Atenção:** em muitas T95 a depuração aceita conexão **sem pedir autorização**. Deixe ligada só durante a sessão;
o assistente desliga no final.

## Passo 0.5: chave do TMDB (só para a tela inicial com recomendações)

1. Crie uma conta em https://www.themoviedb.org/signup.
2. Vá em **Configurações → API** (https://www.themoviedb.org/settings/api) e peça uma chave de uso pessoal.
3. Guarde o **"Token de leitura da API"** (começa com `eyJ...`). Você vai colá-lo num arquivo quando o
   assistente pedir. Não cole a chave em lugares públicos.

> A chave fica gravada na box. Numa box que já teve malware, outro app poderia lê-la. O máximo que alguém faria com
> ela é consultar o TMDB em seu nome (ela só tem acesso de leitura); se desconfiar, gere uma chave nova no site.
> Se preferir não arriscar, peça a tela inicial **simples, sem internet** (o launcher do próprio CleanYourTV).

---

## Passo 1: cole o texto abaixo no assistente

Troque `<IP DA BOX>` pelo IP anotado.

```text
Tenho uma TV box Android genérica (T95 / clone Amlogic) no IP <IP DA BOX>, na mesma rede deste computador, com a
Depuração USB ligada. Quero limpar os apps, fazer o botão de desligar do controle desligar de verdade e trocar a
tela inicial. Fale comigo em português, trabalhe passo a passo e me pergunte antes de qualquer coisa que dependa
da minha escolha.

Ferramentas e regras
- Clone https://github.com/Gathin23/cleanyourtv e use tools/tv.py (numa venv com tools/requirements.txt),
  tools/adb-closed.py e o que mais o repositório oferecer. Leia AGENTS.md e PROMPT.md e siga as regras de
  segurança deles.
- Nunca use root nem su, mesmo que exista. Nunca desinstale pacotes de SISTEMA (só desativar, e só com meu ok).
  Nunca abra pacotes de fábrica ou de teste.
- Comece salvando um backup em backup/ (pm list packages -f/-d/-3, getprop, settings global/secure/system).
- Não coloque meu IP, MAC, nome de rede ou chave em nada que eu possa publicar.

1. Apps
   - Liste os apps instalados pelo usuário (pm list packages -3) com nome legível, versão e data de instalação,
     agrupados em: lojas/instaladores fora da Play Store, IPTV não oficiais, streaming, utilitários, outros.
     Marque com um aviso as lojas alternativas e os IPTV não oficiais (são a porta de entrada mais comum para
     malware nessas boxes).
   - Me deixe escolher o que remover. Antes de remover um launcher, confira (cmd package query-activities
     -a android.intent.action.MAIN -c android.intent.category.HOME) que sobra outro para servir de reserva.
   - Remova com pm uninstall só o que eu escolher, e mostre o que sobrou.
   - Procure arquivos .apk, .xapk, .apks e .apkm em /storage/emulated/0/ (Download e pastas de apps) e em
     pendrives em /storage/. Mostre a lista e, com meu ok, apague-os e remova as pastas que ficarem vazias.
     Avise sobre pastas de dados grandes deixadas por apps removidos, sem apagar sem eu pedir.

2. Botão de desligar
   - Em boxes Amlogic/Droidlogic, o app com.droidlogic.tv.settings tem a opção "Powerkey definition", gravada em
     Settings.System "power_key_definition": 0 = suspender, 1 = desligar, 2 = reiniciar. Confira isso na sua
     box: leia o valor atual (settings get system power_key_definition) e, se puder, confirme o mapeamento no
     app de configurações (aapt/dexdump no APK puxado de /vendor/app/DroidTvSettings, sem iniciar nada).
   - Anote o valor antigo, mude para 1 (settings put system power_key_definition 1) e me peça para apertar o
     botão de desligar do controle DE VERDADE (tecla injetada por ADB não prova nada). Se não funcionar, volte
     o valor antigo e investigue a tecla com getevent -lt.

3. Tela inicial
   - Confira o SDK (getprop ro.build.version.sdk) e me pergunte qual quero:
     a) CleanYourTV UI (https://github.com/matheus-caldeira/cleanyourtv-ui): estilo Apple TV, recomendações do
        TMDB, precisa da minha chave do TMDB e de Android 9 (SDK 28) ou mais novo;
     b) o launcher simples do próprio cleanyourtv (launcher/), sem internet, Android 8 (SDK 26) ou mais novo.
   - Para a), clone o repositório ao lado do cleanyourtv, leia o AndroidManifest.xml (permissões) e os hosts
     de rede no código antes de compilar, e me resuma. Compile a partir do código (launcher-ui/build.sh; precisa
     de JDK 17 e Android SDK com build-tools 34 e platform android-34; me peça permissão antes de baixar o SDK
     e aceitar as licenças). Não instale APK pronto baixado da internet.
   - Prepare um config.json a partir de launcher-ui/config.example.json com tmdb_region "BR", language "pt",
     a minha cidade para o clima e a prateleira (shelf) com os apps que eu escolher. Deixe "tmdb_key" vazio e
     me peça para colar a chave no arquivo; nunca mostre a chave no chat ou nos logs.
   - Instale com launcher-ui/build-and-install.sh (STOCK_LAUNCHER=none para não desativar nada e manter o
     launcher atual como reserva) e envie o config com launcher-ui/config.sh. No Windows/Git Bash, passe os
     caminhos de APK e CONFIG no formato C:/... .
   - Verifique: tela inicial com dumpsys activity activities (mResumedActivity), capturas com
     tools/tv.py --screenshot, navegação com o direcional, abrir um título e voltar. Reinicie e confira que a
     tela nova volta sozinha. Me peça para apertar o botão Home do controle de verdade.
   - Se o protetor de tela anterior era de um pacote suspeito, me avise de que foi trocado.

4. No final
   - Escreva work/LEIA-ME.md com o que mudou e como desfazer cada parte (voltar o launcher reserva com
     cmd package set-home-activity, valor antigo do botão de desligar), sem IP, MAC, rede ou chave.
   - Feche o ADB: settings put global adb_enabled 0, confirme com tools/adb-closed.py <IP DA BOX> que a porta
     5555 está FECHADA, me peça para desligar e ligar a box e confira de novo. Só o "closed" da ferramenta vale.
```

---

## Depois

- Mudar a prateleira ou a cidade: edite o `config.json` e rode `launcher-ui/config.sh` com a depuração ligada.
- Voltar à tela inicial antiga: `cmd package set-home-activity` com o launcher reserva (o assistente deixa o
  comando no `LEIA-ME.md`).
- Sempre desligue a depuração ao terminar.
