# Relatório de Análise de Threads - Escalonamento de Tarefas

**Aluno:** Asaph Arruda  
**Data de Nascimento:** 14/12/2004  
**Disciplina:** Sistemas Operacionais (2025.1)  
**Professor:** Leonardo A. Minora

---

## 1. Objetivo

O objetivo desta atividade é explorar e analisar o comportamento do sistema operacional no escalonamento de tarefas, através da criação e monitoramento de threads do tipo CPU-bound (intensivas em processamento) e I/O-bound (intensivas em operações de entrada/saída).

A proposta inclui:
- Modificar um programa em C que cria threads distintas.
- Observar o comportamento dessas threads em execução.
- Utilizar ferramentas de monitoramento e análise de desempenho.
- Comparar a diferença no uso de recursos entre os dois tipos de tarefas.

---

## 2. Ambiente de Execução

A atividade foi desenvolvida em um contêiner Docker com a imagem Fedora personalizada para a disciplina. As ferramentas utilizadas incluem:

- `gcc` para compilação do código C
- `htop`, `top`, `ps`, `time` e `perf stat` para análise e monitoramento
- Shell `fish` dentro do contêiner

---

## 3. Modificações no Código

O código original foi adaptado conforme os seguintes pontos:

- Adicionada uma **nova thread CPU-bound**, totalizando 3.
- Adicionada uma **nova thread I/O-bound**, totalizando 3.
- Alterado o cálculo da thread CPU-bound para estimativa de **π (Pi)** via série de Leibniz:

```c
long n = 100000000;
double pi = 0.0;
for (long i = 0; i < n; i++) {
    pi += (i % 2 == 0 ? 1.0 : -1.0) / (2 * i + 1);
}
pi *= 4;
printf("Thread CPU-bound %ld terminou (π ≈ %f)\n", (long)arg, pi);
```

## 4. Execução e Resultados

### 4.1 Compilação
```c
gcc -o testeThreads threads_cpu_io.c -lpthread -lm
```
### 4.2 Execução do Programa
```c
./testeThreads

```
### **Resultado:**

```c
Processo pai (PID: 84)

Processo filho (PID: 85)
Thread CPU-bound 1 iniciada (PID: 85)
Thread CPU-bound 2 iniciada (PID: 85)
Thread I/O-bound 1 iniciada (PID: 85)
Thread I/O-bound 2 iniciada (PID: 85)
Thread CPU-bound 1 terminou (π ≈ 3.141593)
Thread CPU-bound 2 terminou (π ≈ 3.141593)
Thread I/O-bound 2 terminou
Thread I/O-bound 1 terminou

```
### 4.3 Monitoramento com time
```c
time ./threads_cpu_io
```
### **Resultado:**
```c
Executed in    4.08 millis    fish           external
usr time       0.00 micros
sys time     940.00 micros
```

### 4.4 Monitoramento com perf stat
```c
perf stat ./threads_cpu_io
```

### **Resultado:**
```c
Error:
No permission to enable cycles event.
```

## 5. Comparações Observadas
| Tipo de Thread | Comportamento                    | Consumo de CPU | Tempo de Espera |
| -------------- | -------------------------------- | -------------- | --------------- |
| CPU-bound      | Executa cálculos intensivos (π)  | Alto           | Baixo           |
| I/O-bound      | Executa `sleep(2)` simulando I/O | Baixo          | Alto            |


Durante a execução simultânea, é possível observar que:

As threads CPU-bound consomem mais ciclos da CPU e continuam ativas durante toda a execução.

As threads I/O-bound entram em estado de espera rapidamente (em sleep) e consomem pouquíssimos recursos da CPU.

## 6. Conclusão
A atividade permitiu a aplicação prática dos conceitos de escalonamento de tarefas em sistemas operacionais. Ao modificar e executar o programa com múltiplas threads de tipos distintos, foi possível observar claramente como o sistema operacional lida com diferentes cargas de trabalho.

As threads CPU-bound exigem mais do processador, permanecendo em execução ativa até o fim dos cálculos. Já as threads I/O-bound rapidamente entram em espera, liberando a CPU para outras tarefas. Isso demonstra a importância de um bom algoritmo de escalonamento para manter o desempenho e a responsividade do sistema.

O monitoramento com ferramentas como htop, top, time e perf mostrou-se essencial para entender como o sistema gerencia recursos, mesmo que algumas limitações de permissão tenham impedido o uso completo de certas ferramentas como o perf.
