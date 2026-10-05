---
tipo: seguranca
titulo: 27. Seguranca do app mobile
projeto: [todos]
stack: [flutter, dart]
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [mobile, flutter, app, apk, descompilar, secure storage, keychain, keystore, certificate pinning, root, jailbreak, ofuscacao, android]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 27. Seguranca do app mobile
## Resumo
O APK esta na mao do atacante: nada de segredo embutido, token em armazenamento seguro e a decisao sempre no servidor.

## Contexto
Regra 27. Dois apps Flutter: **Heavy Drive** ([[Heavy]], offline-first, Android) e o app do
[[ICook]]. O ICook ja declara `flutter_secure_storage` no `pubspec.yaml`; o app do Heavy ainda
nao chegou nessa parte.

## Detalhe

### A premissa que muda tudo
Um APK e um arquivo que o usuario baixa. Ele **descompila** (`apktool`, `jadx`) em minutos.
Tudo que esta dentro e publico: string, constante, endpoint, logica de validacao.

Isso torna a regra [[01-esconder-api-keys]] mais dura no mobile: `--dart-define` mantem a chave
fora do repositorio, **mas nao fora do binario**. Segredo de verdade nao vai para o app; fica no
servidor, atras de um endpoint autenticado.

### Armazenamento
| Onde | Serve para |
| --- | --- |
| `flutter_secure_storage` (Keychain / Keystore) | token, refresh token, credencial |
| `shared_preferences` | tema, idioma, ultimo filtro — **nunca** token |
| banco local (Drift/sqflite) | cache offline; criptografe se guardar dado pessoal |

O Heavy Drive e offline-first e vai guardar **entrega, assinatura e localizacao** no dispositivo.
Isso e dado pessoal em aparelho que pode ser perdido ou roubado — o banco local precisa de
criptografia e de limpeza depois da sincronizacao.

### Comunicacao
- Só HTTPS, sem excecao de dev vazando para release
  (cuidado com `network_security_config.xml` liberando cleartext)
- **Certificate pinning** para a API principal; com plano de rotacao, senao a troca do
  certificado derruba a base instalada inteira
- Nunca aceitar certificado invalido, nem "so em debug" — esse `if` vai para producao um dia

### Plataforma
- `flutter build apk --obfuscate --split-debug-info=...` — atrapalha, nao impede
- Detectar root/jailbreak como **sinal**, nunca como barreira
- Bloquear screenshot em tela sensivel (`FLAG_SECURE`)
- Nao logar payload em release (`kReleaseMode`)
- Revisar permissoes do `AndroidManifest`: localizacao em background e sensivel e exige
  justificativa na loja

### A regra que resume
Validacao no app e experiencia do usuario. **Toda decisao que importa acontece no servidor** —
[[06-auth-no-servidor]]. Se o app decide sozinho quem pode o que, um APK modificado decide
diferente.

## Relacionado
- [[06-auth-no-servidor]]
- [[25-ciclo-de-vida-da-sessao]]
- [[flutter]]
