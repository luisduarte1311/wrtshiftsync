# WrtShiftSync 📱✨

Bem-vindo ao portal privado de sincronização de horários! Esta ferramenta foi concebida para simplificar a organização pessoal: pega no ficheiro PDF da escala de trabalho e transforma-o automaticamente num formato de calendário padrão (`.ics`), pronto a integrar em qualquer dispositivo móvel de forma rápida, privada e sem atritos.

---

## 🔒 Parte 1: Para quem usa (Simplicidade, Privacidade & Multiplataforma)

A privacidade e a soberania sobre os teus dados são prioridades absolutas. Esta aplicação foi desenhada sob uma filosofia de **confiança zero para o exterior e controlo total para o utilizador**:

- **Onde param os meus dados?** Em lado nenhum. O PDF carregado é processado em tempo real e **eliminado de imediato** do servidor assim que o calendário é gerado. Não existe armazenamento persistente, registo de histórico ou partilha com terceiros.
- **Conformidade RGPD & Transparência:** Todo o processamento ocorre estritamente a pedido explícito e voluntário do titular dos dados para efeitos imediatos de sincronização de calendário.
- **Quem tem acesso?** O acesso ao pipeline é restrito e protegido por autenticação baseada em chave alfanumérica, mantendo o serviço blindado contra acessos não autorizados.
- **Compatibilidade Multiplataforma Inteligente:**
  - **iOS (iPhone / iPad):** A interface deteta o Safari e orienta o utilizador a adicionar a ferramenta diretamente ao ecrã principal como uma Web App (PWA) de acesso instantâneo.
  - **Android & Outros:** Suporta navegação web universal e integra referências às opções de sincronização nativa (incluindo o ecossistema de aplicações complementares dedicadas).
- **Como funciona na prática?**
  1. Acedes ao link seguro da plataforma.
  2. Introduzes a chave de acesso autorizada.
  3. Selecionas o PDF da escala mensal (*os dados são processados de forma volátil e segura*).
  4. Escolhes o teu perfil e descarregas o ficheiro `.ics` para sincronização imediata com o teu calendário.

---

## ⚙️ Parte 2: Arquitetura Técnica & Stack Open-Source (Power Users & Admin)

Esta secção documenta os pilares tecnológicos do backend, a infraestrutura de self-hosting e o ecossistema de dependências.

### 1. Stack Tecnológica de Código Aberto (Permissive Licenses)
O motor do pipeline é construído inteiramente sobre ferramentas de código aberto maduras e seguras, licenciadas sob normas altamente permissivas (MIT, Apache 2.0 e BSD), garantindo total liberdade de execução técnica sem contaminação de licenças copyleft:
- **Backend & API:** **FastAPI** (`0.110.0` - MIT) e **Uvicorn** (`0.28.0` - BSD 3-Clause) para alta performance assíncrona.
- **Motor de Extração de PDF:** **pdfplumber** (`0.10.3` - MIT) para parsing estruturado de tabelas de horário.
- **Geração de Calendário:** **ics** (`0.7.2` - Apache 2.0) para criação de eventos conformes com a norma RFC 5545 (com UIDs determinísticos anti-duplicação).
- **Templates & UI:** **Jinja2** (`3.1.3` - BSD 3-Clause) e **Tailwind CSS** para uma interface responsiva nativa.

### 2. Infraestrutura & Segurança de Servidor (*Self-Hosted*)
- **Ambiente de Execução:** Contentorização isolada via Docker e WSL2 em hardware dedicado de operação contínua.
- **DNS Dinâmico:** Gestão de endereçamento residencial resiliente via **DuckDNS**.
- **Proxy Reverso & Encriptação:** **Nginx Proxy Manager (NPM)** para terminação de SSL/TLS (certificados Let's Encrypt) e blindagem das portas de rede.
- **Isolamento de Processos:** Execução sob redes Docker bridge dedicadas e princípio do privilégio mínimo.

---

## ⚖️ Parte 3: Aviso Legal & Propriedade Intelectual (Legal Disclaimer)

© **2026 Luís Duarte. Todos os direitos reservados.**

- **Natureza Independente:** O **WrtShiftSync** é uma ferramenta utilitária independente, concebida e desenvolvida de forma estritamente pessoal, em âmbito privado, fora do horário laboral e sem utilização de recursos ou diretivas de entidades empresariais.
- **Isenção de Afiliação:** Este projeto não é endossado, patrocinado ou oficialmente afiliado a nenhuma corporação ou marca registada comercial.
- **Proteção de Propriedade Intelectual:** Todos os direitos relativos à conceção do pipeline, arquitetura de parsing, código proprietário de suporte e identidade visual pertencem exclusivamente ao autor. A engenharia reversa, redistribuição não autorizada, descompilação ou integração corporativa sem consentimento prévio e expresso são estritamente proibidas nos termos da legislação aplicável.
