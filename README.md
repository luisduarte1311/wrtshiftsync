# WrtShiftSync (iOS) 📱✨

Bem-vindo ao portal privado de sincronização de horários! Esta ferramenta foi criada para simplificar a tua vida: pega no horário em formato PDF da empresa e transforma-o automaticamente num ficheiro de calendário pronto a instalar no teu iPhone, de forma 100% segura, privada e sem complicações.

---

## 🔒 Parte 1: Para quem usa (Simplicidade, Privacidade e Segurança)

Sabemos que a privacidade dos teus dados pessoais e profissionais é sagrada. Esta aplicação foi desenhada com uma filosofia de **confiança zero para o exterior e controlo total para ti**:

* **Onde param os meus dados?** Em lado nenhum. O PDF que carregas é processado no momento e **eliminado de imediato** do servidor assim que o teu calendário é gerado. Nada é guardado, armazenado ou partilhado com terceiros.
* **Quem tem acesso?** Apenas quem pertencer ao nosso grupo e possuir a **chave alfanumérica de acesso**. Isto mantém a ferramenta protegida contra estranhos, robôs e curiosos na internet.
* **Porque é seguro no iOS?** Toda a ligação utiliza encriptação de ponta a ponta (HTTPS com certificado verificado), o que significa que o Safari protege a tua comunicação desde o primeiro clique.
* **Como funciona na prática?** 
  1. Entras no link seguro (`https://wrtshiftsync-ios.duckdns.org`).
  2. Introduzes a palavra-passe/chave da loja.
  3. Carregas o PDF do mês.
  4. Descarregas o teu calendário pessoal e adicionas os turnos ao iPhone com um único toque. Simples, rápido e sem apps de terceiros à espreita.

---

## ⚙️ Parte 2: Arquitetura Técnica & Infraestrutura (Power Users & Admin)

Esta secção documenta os pilares técnicos, o stack de infraestrutura e as regras de isolamento que mantêm o servidor a correr de forma autónoma e segura em regime self-hosted.

### 1. Stack Tecnológica
* **Ambiente de Execução:** Docker Desktop sobre WSL2 (Linux) num servidor doméstico dedicado 24/7.
* **DNS Dinâmico:** Gestão automática de IP residencial via **DuckDNS** (`wrtshiftsync-ios.duckdns.org`).
* **Proxy Reverso & SSL:** **Nginx Proxy Manager (NPM)** para terminação de HTTPS, emissão de certificados Let's Encrypt e reforço de segurança nas portas `80` e `443`.
* **Isolamento de Rede:** Comunicação interna estritamente restrita a uma rede Docker bridge dedicada (`wrtshift-net`).

### 2. Segurança e Controlo de Acessos
* **Autenticação Baseada em Segredo:** Validação prévia de credenciais alfanuméricas injetadas via variáveis de ambiente (`Environment Variables`), impedindo qualquer acesso não autorizado antes de expor rotas de processamento.
* **Isolamento de Processos:** Execução de contentores sob o princípio do privilégio mínimo, mantendo o motor de parsing (`pdfplumber` em Python) isolado do sistema hospedeiro.
