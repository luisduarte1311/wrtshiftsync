# WrtShiftSync 📱✨

Bem-vindo ao portal privado de sincronização de horários! Esta ferramenta foi concebida para simplificar a organização pessoal: pega no ficheiro PDF da escala de trabalho e transforma-o automaticamente num formato de calendário padrão (`.ics`), pronto a integrar em qualquer dispositivo (telemóvel, tablet ou computador) de forma rápida, privada e sem atritos.

---

## 🔒 Parte 1: Para quem usa (Simplicidade, Privacidade & Multiplataforma)

A privacidade e a soberania sobre os teus dados são prioridades absolutas. Esta aplicação foi desenhada sob uma filosofia de **confiança zero para o exterior e controlo total para o utilizador**:

- **Onde param os meus dados?** O PDF carregado é processado em tempo real e **eliminado de imediato** do servidor assim que a leitura termina. Os dados extraídos para gerar o calendário ficam apenas em memória volátil (nunca em disco) e perdem-se quando o serviço reinicia. Os horários não são guardados em base de dados, não existe histórico e nada é partilhado com terceiros.
- **Conformidade RGPD & Transparência:** Todo o processamento ocorre estritamente a pedido explícito e voluntário do titular dos dados para efeitos imediatos de sincronização de calendário.
- **Quem tem acesso?** O acesso ao pipeline é restrito e protegido por autenticação baseada em chave alfanumérica, com limitação do número de pedidos por minuto, mantendo o serviço blindado contra acessos não autorizados e tentativas de força bruta.
- **Compatibilidade Multiplataforma Inteligente:** Não é uma ferramenta só para iPhone. A interface adapta as instruções ao dispositivo:
  - **iPhone / iPad (Safari):** orienta o utilizador a adicionar a ferramenta ao ecrã principal como Web App (PWA) de acesso instantâneo.
  - **iPhone / iPad (outro browser):** apresenta as instruções adequadas a esse caso.
  - **Android:** suporta navegação web universal e a importação do `.ics` no calendário do dispositivo.
  - **Computador:** funciona em qualquer browser moderno, com o `.ics` a abrir na aplicação de calendário instalada.
- **Como funciona na prática?**
  1. Acedes ao link seguro da plataforma.
  2. Introduzes a chave de acesso autorizada.
  3. Selecionas o PDF da escala mensal (*os dados são processados de forma volátil e segura*).
  4. Escolhes o teu perfil e descarregas o ficheiro `.ics` para sincronização imediata com o teu calendário (Apple Calendário, Google Calendar, Outlook ou qualquer aplicação que aceite o formato iCalendar).

---

## ⚙️ Parte 2: Arquitetura Técnica & Stack Open-Source (Power Users & Admin)

Esta secção documenta os pilares tecnológicos do backend, a infraestrutura de self-hosting e o ecossistema de dependências.

### 1. Stack Tecnológica de Código Aberto
As bibliotecas Python do motor do pipeline usam licenças permissivas (MIT, Apache 2.0 e BSD), garantindo liberdade de execução técnica sem contaminação de licenças copyleft:
- **Backend & API:** **FastAPI** (`0.110.0` - MIT) e **Uvicorn** (`0.28.0` - BSD 3-Clause) para alta performance assíncrona, com **python-multipart** (`0.9` - Apache 2.0) para o upload e **slowapi** (MIT) para a limitação de pedidos.
- **Motor de Extração de PDF:** **pdfplumber** (`0.10.3` - MIT) para parsing estruturado de tabelas de horário, seguido de uma validação final de integridade dos dados extraídos.
- **Geração de Calendário:** **ics** (`0.7.2` - Apache 2.0) para criação de eventos conformes com a norma RFC 5545 (com UIDs determinísticos anti-duplicação).
- **Templates & UI:** **Jinja2** (`3.1.3` - BSD 3-Clause) e **Tailwind CSS** (MIT) para uma interface responsiva nativa.
- **Serviço auxiliar de webhooks:** **Flask** (BSD 3-Clause) com **Flask-Limiter** (MIT), integrado com a **Evolution API** (`v2.3.7`) e **PostgreSQL** (`15`), para a gestão de pedidos de acesso através do WhatsApp.

### 2. Infraestrutura & Serviços (*Self-Hosted*)
O projeto corre como uma stack **Docker Compose** com seis serviços numa rede interna dedicada:
- **Web app (FastAPI/Uvicorn):** o motor de extração e geração do `.ics`.
- **Proxy Reverso & Encriptação:** **Nginx Proxy Manager (NPM)** para terminação de SSL/TLS (certificados Let's Encrypt) e blindagem das portas de rede.
- **DNS Dinâmico:** gestão de endereçamento residencial resiliente via **DuckDNS**.
- **Evolution API + PostgreSQL:** integração com o WhatsApp; a base de dados serve exclusivamente esta integração e **não guarda horários**.
- **Serviço de webhooks (Flask):** recebe eventos do grupo de acessos, regista o estado de cada pedido e notifica os administradores.
- **Ambiente de Execução:** contentorização isolada via Docker e WSL2 em hardware dedicado de operação contínua.

### 3. Segurança & Configuração
- **Segredos fora do código:** todas as chaves, tokens e passwords são lidos de um ficheiro `.env` que nunca é versionado, e o código não tem valores por defeito para segredos.
- **Controlo de acesso:** autenticação por chave alfanumérica e limitação de pedidos por minuto, mais restritiva nos pontos de upload e validação da chave.
- **Dados voláteis:** o PDF é guardado num ficheiro temporário eliminado logo após a leitura, e os dados extraídos existem apenas em memória.
- **Dados de infraestrutura fora do Git:** sessões do WhatsApp, base de dados e volumes do proxy não são versionados.
- **Isolamento de Processos:** execução sob redes Docker bridge dedicadas.

### 4. Estrutura do Repositório
```
main.py              Web app (FastAPI): upload do PDF, validação da chave, descarga do .ics
parser.py            ScheduleParser: extração e validação dos turnos a partir do PDF
generator.py         CalendarGenerator: geração do ficheiro .ics
templates/           Interface web (index.html)
app.py               Serviço Flask de webhooks (integração WhatsApp / Evolution API)
whatsapp-bot/        Cliente WhatsApp experimental (Baileys), fora da stack Docker
Dockerfile           Imagem Python usada pela web app e pelo serviço de webhooks
docker-compose.yml   Definição da stack completa
requirements.txt     Dependências Python
```

### 5. Execução (administradores)
1. Criar um ficheiro `.env` na raiz do projeto com os segredos necessários (nunca o enviar para o Git).
2. Arrancar a stack: `docker compose up -d --build`

---

## ⚖️ Parte 3: Aviso Legal & Propriedade Intelectual (Legal Disclaimer)

© **2026 Luís Duarte. Todos os direitos reservados.**

- **Natureza Independente:** O **WrtShiftSync** é uma ferramenta utilitária independente, concebida e desenvolvida de forma estritamente pessoal, em âmbito privado, fora do horário laboral e sem utilização de recursos ou diretivas de entidades empresariais.
- **Isenção de Afiliação:** Este projeto não é endossado, patrocinado ou oficialmente afiliado a nenhuma corporação ou marca registada comercial.
- **Proteção de Propriedade Intelectual:** Todos os direitos relativos à conceção do pipeline, arquitetura de parsing, código proprietário de suporte e identidade visual pertencem exclusivamente ao autor. A engenharia reversa, redistribuição não autorizada, descompilação ou integração corporativa sem consentimento prévio e expresso são estritamente proibidas nos termos da legislação aplicável.
