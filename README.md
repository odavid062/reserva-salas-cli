# reserva-salas-cli

Sistema de reserva de salas de reunião via **terminal (CLI)** — menu interativo que permite criar salas, fazer reservas, listar e cancelar, com persistência em banco PostgreSQL via Spring Boot e JPA.

[![Java](https://img.shields.io/badge/Java-21-orange?logo=openjdk)](https://openjdk.org/projects/jdk/21/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2.2-brightgreen?logo=springboot)](https://spring.io/projects/spring-boot)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-blue?logo=postgresql)](https://www.postgresql.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Como funciona

```mermaid
flowchart TD
    Start([Iniciar aplicação]) --> Menu

    Menu -->|1| MR[Fazer reserva]
    Menu -->|2| LR[Listar reservas]
    Menu -->|3| CR[Cancelar reserva]
    Menu -->|4| CS[Criar sala]
    Menu -->|0| Exit([Encerrar])

    MR --> BS1[BookingService.makeReservation]
    BS1 --> RR1{Sala existe?}
    RR1 -->|Sim| Save[Salva reserva no banco]
    RR1 -->|Não| Err1[Sala não encontrada]

    CR --> BS2[BookingService.cancelReservation]
    BS2 --> RR2{Reserva existe?}
    RR2 -->|Sim| Del[Deleta reserva]
    RR2 -->|Não| Err2[Reserva não encontrada]

    CS --> BS3[BookingService.createRoom]
    BS3 --> RR3{Nome único?}
    RR3 -->|Sim| SaveRoom[Salva sala no banco]
    RR3 -->|Não| Err3[Sala já existe]

    LR --> BS4[BookingService.listReservations]
    BS4 --> Print[Imprime todas as reservas]
```

---

## Funcionalidades

- **Criar sala** — cadastra sala com nome único (validação de duplicata)
- **Fazer reserva** — associa usuário a uma sala existente
- **Listar reservas** — exibe todas as reservas ativas
- **Cancelar reserva** — remove reserva pelo nome do usuário e da sala

---

## Stack

| Tecnologia | Versão | Uso |
|---|---|---|
| Java | 21 | Linguagem principal |
| Spring Boot | 3.2.2 | Injeção de dependência e contexto |
| Spring Data JPA | — | Persistência ORM |
| PostgreSQL | — | Banco de dados relacional |
| Lombok | 1.18.36 | Redução de boilerplate |

> **Por que Spring Boot em uma CLI?**  
> O Spring gerencia injeção de dependência e a camada de persistência (JPA/Hibernate), eliminando configuração manual. A interface de entrada é o terminal, mas toda a lógica de banco é gerenciada pelo framework.

---

## Como Executar

### Pré-requisitos
- Java 21
- PostgreSQL rodando localmente
- Banco `reserva_sala` criado

```sql
CREATE DATABASE reserva_sala;
```

### Configuração

Crie um arquivo `.env` ou defina as variáveis de ambiente:

```bash
export DB_URL=jdbc:postgresql://localhost:5432/reserva_sala
export DB_USERNAME=postgres
export DB_PASSWORD=sua_senha
```

Ou edite diretamente o `application.properties` para desenvolvimento local.

### Executar

```bash
git clone https://github.com/odavid062/atvRafael2.git
cd atvRafael2
./mvnw spring-boot:run
```

O menu interativo será exibido no terminal:

```
1 - Fazer reserva
2 - Listar reservas
3 - Cancelar reserva
4 - Criar sala
0 - Sair
Escolha uma opção:
```

---

## Estrutura do Projeto

```
src/main/java/org/example/
├── Main.java                          # Ponto de entrada + menu CLI (Scanner)
├── Model/
│   ├── Room.java                      # Entidade sala (nome único)
│   └── Reservation.java               # Entidade reserva (sala + usuário)
├── Repository/
│   ├── RoomRepository.java            # findByName, existsByName
│   └── ReservationRepository.java     # findByRoomNameAndUser
└── Service/
    └── BookingService.java            # Regras: reservar, cancelar, listar, criar sala
```

---

## Observações Técnicas

- `@Column(name = "username")` na entidade `Reservation` — evita conflito com a palavra reservada `user` no PostgreSQL
- Validação de unicidade da sala feita antes de persistir (`existsByName`)
- Constructor injection no `BookingService` (sem `@Autowired` em campo)

---

## Autor

**David Rodrigues**
[![GitHub](https://img.shields.io/badge/GitHub-odavid062-181717?logo=github)](https://github.com/odavid062)
