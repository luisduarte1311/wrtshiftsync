# Arquitetura Conceptual: Portal de Extração de Horários (iOS & Servidor Caseiro)

Este documento resume a arquitetura técnica, o fluxo de utilizador e os pilares de segurança definidos para permitir que utilizadores de iOS (sem perfil técnico e sem ligação ao ecossistema Google) extraiam o seu horário individual a partir de um ficheiro PDF mensal e o adicionem diretamente ao calendário nativo do iPhone, utilizando infraestrutura 100% gratuita e alojada localmente.

---

## 1. Visão Geral do Sistema

A solução assenta numa arquitetura híbrida **Cliente Leve (iOS Safari / PWA) + Motor de Processamento Isolado (Servidor Caseiro Docker)**:

* **Custo Total:** 0 € (sem contas Apple Developer, sem domínios pagos e sem serviços cloud pagos).
* **Independência de Ecossistema:** Focado na geração de ficheiros standard iCal (`.ics`) compatíveis nativamente com o Calendário da Apple (iOS).
* **Aproveitamento de Recursos:** Reutiliza o script Python existente (baseado em `pdfplumber`) a correr num ambiente virtualizado/isolado (Docker / WSL2) num servidor doméstico ligado 24/7.

---

## 2. Fluxo de Utilizador (UX End-to-End no iOS)

O processo foi desenhado para ser livre de fricção técnica para os trabalhadores da loja (sem necessidade de instalar VPNs ou apps de terceiros):

1. **Receção do PDF:** O funcionário recebe o PDF do horário mensal completo da loja (via WhatsApp, Email ou outra plataforma).
2. **Acesso ao Portal:** Abre o Safari no iPhone e acede a um link fixo e amigável (gerido via DuckDNS).
3. **Autenticação Rápida:** O portal solicita um **PIN/Password alfanumérica** partilhada da loja antes de permitir qualquer interação.
4. **Upload do Ficheiro:** Após validação do PIN, o utilizador seleciona e submete o ficheiro PDF diretamente do telemóvel.
5. **Processamento no Servidor:** O contentor Docker recebe o PDF, valida-o e executa o script Python (`pdfplumber`), extraindo todos os funcionários e respetivos turnos/pausas/folgas.
6. **Seleção Individual:** O utilizador escolhe o seu nome ou número mecanográfico a partir da lista devolvida pelo servidor.
7. **Geração e Importação no Calendário:**
   * O servidor gera dinamicamente um ficheiro `.ics` contendo apenas os turnos válidos desse trabalhador (ignorando folgas como `FC`, `FO` ou dias vazios `V`).
   * O iOS reconhece o ficheiro `.ics` e abre automaticamente o pop-up nativo do Calendário da Apple para adicionar todos os eventos com um toque.

---

## 3. Infraestrutura e Rede (Custo Zero)

* **Endereço Fixo Gratuito (DDNS):** Utilização do **DuckDNS** para fornecer um subdomínio permanente (ex: `horarios-loja.duckdns.org`) apontado ao servidor doméstico, atualizado automaticamente por um contentor Docker leve.
* **Gestão de Tráfego e SSL:** Utilização de um Reverse Proxy em Docker (como o **Nginx Proxy Manager**) para encaminhar pedidos externos para a aplicação e gerar certificados HTTPS gratuitos (via Let's Encrypt), garantindo que o Safari do iOS não bloqueia a ligação.
* **Runtime da Aplicação:** Um contentor Docker dedicado (ex: Python com Flask ou FastAPI) a correr sobre WSL2/Linux no computador servidor 24/7, contendo o motor de parsing `pdfplumber`.

---

## 4. Arquitetura de Segurança e Isolamento (Pilares Obrigatórios)

Para garantir que a exposição de um serviço doméstico à internet não compromete a rede interna nem o computador anfitrião, aplicam-se três camadas rigorosas de defesa:

### 4.1. Isolamento Total de Rede (Ambiente Virtualizado)
* **Rede Docker Dedicada:** A aplicação web e o script de processamento correm numa rede Docker isolada (`bridge` customizada sem acesso à rede local `LAN`).
* **Zero Acesso ao Host:** O contentor não tem volumes mapeados para pastas pessoais do sistema anfitrião (apenas um diretório temporário efémero para processamento em memória/disco isolado) e não consegue comunicar com outros dispositivos da rede doméstica.
* **Princípio do Privilégio Mínimo:** O processo Python dentro do contentor corre com um utilizador sem privilégios de administrador (`non-root user`).

### 4.2. Controlo de Acesso e Autenticação (Barreira de Entrada)
* **Bloqueio Prévio ao Upload:** A interface de upload e os endpoints de processamento estão totalmente bloqueados por defeito.
* **PIN / Password Alfanumérica:** É exigida uma credencial alfanumérica (definida por variável de ambiente no Docker) para desbloquear a sessão. Isto impede o acesso de utilizadores não autorizados e bloqueia varrimentos automáticos de bots na internet.
* **Rate Limiting:** Limitação do número de tentativas falhadas de PIN e do número de pedidos por minuto ao nível do Reverse Proxy.

### 4.3. Validação Estrita de Ficheiros (Proteção contra Exploits)
* **Verificação de Tipo e Assinatura (MIME / Magic Bytes):** O servidor verifica não só a extensão `.pdf`, mas também o cabeçalho real do ficheiro para garantir que se trata genuinamente de um PDF.
* **Limite de Tamanho:** Restrição rigorosa do tamanho máximo do ficheiro (ex: máximo de 2 MB ou 5 MB, suficiente para os horários mensais) para prevenir ataques de negação de serviço (DoS) por exaustão de disco/memória.
* **Limpeza Automática:** O ficheiro PDF enviado é imediatamente eliminado do contentor assim que a extração dos dados termina, não ficando armazenado no servidor.