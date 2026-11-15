# Arquitetura Conceptual: wrtshiftsync-ios

Este documento resume a arquitetura técnica, o fluxo de utilizador e os pilares de segurança definidos para permitir a extração e sincronização de horários de trabalho corporativos em formato PDF para o ecossistema iOS, utilizando uma pipeline privada, de custo zero, self-hosted e baseada em Docker.

---

## 1. Visão Geral do Sistema

A solução assenta numa arquitetura híbrida **Cliente Leve (iOS Safari) + Servidor Caseiro Isolado em Docker**:

- **Custo Total:** 0 € (sem subscrições de servidores cloud ou serviços pagos).
- **Independência de Ecossistema:** Geração direta de ficheiros standard iCal (`.ics`) compatíveis nativamente com o Calendário da Apple.
- **Aproveitamento de Recursos:** Integração modular a correr sobre ambiente WSL2 / Docker Desktop num servidor doméstico dedicado.

---

## 2. Fluxo de Utilizador (UX End-to-End no iOS)

O processo foi desenhado para ser fluido e sem fricção técnica para os utilizadores finais no telemóvel:

1. **Receção do PDF:** Obtenção do ficheiro de horário mensal corporativo.
2. **Acesso ao Portal:** Acesso via Safari ao link seguro e amigável (`https://wrtshiftsync-ios.duckdns.org`).
3. **Autenticação por Chave Alfanumérica:** Introdução da palavra-passe/chave estática partilhada para desbloquear o acesso à aplicação.
4. **Submissão do Ficheiro:** Upload do PDF diretamente a partir do dispositivo móvel.
5. **Processamento do Servidor:** Execução do motor em Python (com `pdfplumber`) para parsing e extração de turnos de forma isolada.
6. **Importação no Calendário:** O servidor gera o ficheiro `.ics` otimizado, permitindo ao iOS adicionar os eventos diretamente ao calendário nativo com um toque.

---

## 3. Infraestrutura e Rede

- **DNS Dinâmico:** Utilização do **DuckDNS** (`wrtshiftsync-ios.duckdns.org`) para manter o IP residencial permanentemente mapeado através de um contentor dedicado.
- **Proxy Reverso e SSL:** Gestão de tráfego centralizada via **Nginx Proxy Manager (NPM)**, assegurando terminação HTTPS com certificados Let's Encrypt válidos e forçados nas portas `80` e `443`.
- **Isolamento de Rede:** Comunicação interna compartimentada através de uma rede Docker do tipo bridge dedicada (`wrtshift-net`).

---

## 4. Arquitetura de Segurança e Isolamento (Pilares Zero-Trust)

Para garantir que a exposição do serviço à internet cumpre rigorosos padrões de segurança:

### 4.1. Isolamento de Rede e Execução
- **Rede Privada Docker (`wrtshift-net`):** Os serviços de proxy e aplicação comunicam estritamente numa rede interna isolada da LAN.
- **Princípio do Privilégio Mínimo:** Contentores configurados com restrições e sem exposição desnecessária de portas ao exterior (apenas o proxy gere as entradas públicas).

### 4.2. Controlo de Acesso por Chave Alfanumérica
- **Barreira Pré-Upload:** Nenhuma rota de processamento de ficheiros fica acessível sem a validação prévia de uma chave alfanumérica estática injetada em tempo de execução via variáveis de ambiente do Docker.
- **Sessões Protegidas:** Emissão de cookies seguros para salvaguardar a interatividade do grupo autorizado sem comprometer a segurança global.

### 4.3. Validação e Privacidade de Dados
- **Limpeza Rigorosa:** Os ficheiros PDF submetidos são processados em memória/espaço efémero e imediatamente eliminados após a geração do ficheiro de calendário, garantindo total privacidade.
