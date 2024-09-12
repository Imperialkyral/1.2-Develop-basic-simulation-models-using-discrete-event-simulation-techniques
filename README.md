# 1.2-Develop-basic-simulation-models-using-discrete-event-simulation-techniques
import time
import random
import matplotlib.pyplot as plt
from collections import deque

class Task:
    def __init__(self, name):
        self.name = name
        self.state = "en espera"
        self.start_time = None
        self.end_time = None

    def start(self):
        self.state = "realizando"
        self.start_time = time.time() 

    def complete(self):
        self.state = "finalizado"
        self.end_time = time.time()
        self.duration_time = self.end_time - self.start_time

class TQueue:
    def __init__(self):
        self.queue = deque()
        self.results = []
        self.pending_tasks = []  # Almacenará la cantidad de tareas pendientes después de procesar cada una.
        self.completed_tasks = []  # Almacenará la cantidad de tareas finalizadas después de procesar cada una.

    def add_task(self, task):
        self.queue.append(task)

    def process_tasks(self):
        total_tasks = len(self.queue)
        completed_count = 0
        while self.queue:
            task = self.queue.popleft()
            task.start()
            time.sleep(random.uniform(0.1, 0.2))  # Simula el tiempo que toma completar la tarea
            task.complete()
            self.results.append([task.name, task.duration_time])
            completed_count += 1
            # Guardamos cuántas tareas quedan pendientes y cuántas se han finalizado
            self.pending_tasks.append(len(self.queue))
            self.completed_tasks.append(completed_count)

    def get_results(self):
        return self.results

    def get_pending_tasks(self):
        return self.pending_tasks

    def get_completed_tasks(self):
        return self.completed_tasks

# Crear la cola de tareas y procesar las tareas
tq = TQueue()
for i in range(1, 101):
    tq.add_task(Task(f"Tarea {i}"))

tq.process_tasks()
results = tq.get_results()
pending_tasks = tq.get_pending_tasks()
completed_tasks = tq.get_completed_tasks()

# Ahora creamos el gráfico burndown
plt.figure(figsize=(12, 6))
plt.plot(range(1, len(pending_tasks) + 1), pending_tasks, marker='o', label="Tareas Pendientes", color='blue')
plt.plot(range(1, len(completed_tasks) + 1), completed_tasks, marker='x', label="Tareas Finalizadas", color='green')
plt.title("Gráfico Burndown - Tareas Pendientes vs Tareas Finalizadas")
plt.xlabel("Número de Tareas Procesadas")
plt.ylabel("Cantidad de Tareas")
plt.grid(True)
plt.legend()
plt.show()
