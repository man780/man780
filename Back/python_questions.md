# Кеширование данных в Python-приложениях

## Паттерны

### 1. **Cache-Aside (Вытаскивание из кеша)**
   - **Описание:** При использовании этого паттерна, приложение явно управляет кешем. При запросе данных сначала происходит поиск в кеше, и если данные отсутствуют, они извлекаются из источника данных и затем сохраняются в кеше.
   - **Примеры библиотек:** `cachetools`, `functools.lru_cache`.

### 2. **Write-Through (Прямая запись)**
   - **Описание:** Данные обновляются в кеше сразу же после обновления в основном хранилище. Это гарантирует согласованность данных между кешем и хранилищем.
   - **Примеры библиотек:** `Django Cache Framework`, `SQLAlchemy` с использованием `dogpile.cache`.

### 3. **Write-Behind Caching (Отложенная запись)**
   - **Описание:** Приложение сначала обновляет данные в кеше, а затем асинхронно обновляет основное хранилище. Это уменьшает нагрузку на основное хранилище, ускоряя операции записи.
   - **Примеры библиотек:** Некоторые NoSQL базы данных, такие как Redis, могут поддерживать отложенную запись.

### 4. **Read-Through (Прямое чтение)**
   - **Описание:** При отсутствии данных в кеше, они автоматически извлекаются из основного хранилища и сохраняются в кеше перед возвращением результатов.
   - **Примеры библиотек:** `Django Cache Framework`, `SQLAlchemy` с использованием `dogpile.cache`.

### 5. **Refresh-Ahead (Проактивное обновление)**
   - **Описание:** Предварительное обновление данных в кеше перед истечением их срока действия. Это снижает время ожидания клиента, так как данные уже доступны в кеше.
   - **Примеры библиотек:** `cachetools`.

## Инструменты

### 1. **Memcached**
   - **Описание:** Высокопроизводительная система кеширования в памяти. Широко используется для хранения пар ключ-значение в распределенной среде.
   - **Пример использования:**
     ```python
     import memcache
     mc = memcache.Client(['127.0.0.1:11211'], debug=0)
     mc.set("some_key", "Some value")
     value = mc.get("some_key")
     ```

### 2. **Redis**
   - **Описание:** Ин-memory база данных и система кеширования. Поддерживает различные структуры данных, включая строки, хэши, списки и другие.
   - **Пример использования:**
     ```python
     import redis
     r = redis.StrictRedis(host='localhost', port=6379, db=0)
     r.set('some_key', 'Some value')
     value = r.get('some_key')
     ```

### 3. **Cachetools**
   - **Описание:** Библиотека для кеширования в Python с поддержкой различных стратегий, таких как LRU (Least Recently Used), LFU (Least Frequently Used) и других.
   - **Пример использования:**
     ```python
     from cachetools import Cache, LRUCache
     cache = LRUCache(maxsize=1000)
     cache['key'] = 'value'
     value = cache['key']
     ```

### 4. **Django Cache Framework**
   - **Описание:** Встроенный кеш в фреймворке Django, который поддерживает различные бэкенды для хранения данных в кеше.
   - **Пример использования:**
     ```python
     from django.core.cache import cache
     cache.set('some_key', 'Some value')
     value = cache.get('some_key')
     ```

### 5. **SQLAlchemy с dogpile.cache**
   - **Описание:** SQLAlchemy — библиотека для работы с базами данных, а `dogpile.cache` — это библиотека для общего кеширования в Python.
   - **Пример использования:**
     ```python
     from sqlalchemy.orm import sessionmaker
     from dogpile.cache.api import CacheRegion
     from dogpile.cache.memory import MemoryCacheBackend

     # Создание региона кеша
     cache_region = CacheRegion(
         name='my_cache_region',
         function_key_generator=lambda *args, **kwargs: 'custom_key',
         backend=MemoryCacheBackend(),
     )

     # Использование в SQLAlchemy
     Session = sessionmaker(bind=engine)
     session = Session()

     @cache_region.cache_on_arguments()
     def get_data_from_database(data_id):
         return session.query(MyModel).get(data_id)
     ```



# Вопросы
- Разница между асинхронностью и многопоточностью
- Multiprocessing multithreading разница ?
- Что такое gil, зачем он нужен и как python обходит gil ?
# Ответы

## Разница между Асинхронностью и Многопоточностью

### Асинхронность

**Определение:** Асинхронное программирование - это стиль программирования, который позволяет выполнять другие задачи, в то время как одна задача ожидает завершения какой-то операции, такой как ввод/вывод, вместо блокирования выполнения.

**Пример:**
```python
import asyncio

async def foo():
    print("Start")
    await asyncio.sleep(2)
    print("End")

asyncio.run(foo())
```

### Многопоточность

**Определение:** Многопоточное программирование - это способ выполнения нескольких потоков (небольших подзадач) одновременно, управляемый операционной системой.

**Пример:**
```python
import threading

def print_numbers():
    for i in range(5):
        print(i)

def print_letters():
    for letter in 'ABCDE':
        print(letter)

thread1 = threading.Thread(target=print_numbers)
thread2 = threading.Thread(target=print_letters)

thread1.start()
thread2.start()

thread1.join()
thread2.join()
```

## Multiprocessing vs. Multithreading

### Multiprocessing (Многопроцессорность)

**Определение:** Multiprocessing - это способ использования нескольких процессов, каждый из которых выполняется в отдельной памяти и имеет свой собственный интерпретатор Python.

**Пример:**
```python
from multiprocessing import Process

def foo():
    print("Hello from process!")

if __name__ == '__main__':
    process = Process(target=foo)
    process.start()
    process.join()
```

### Multithreading (Многопоточность)

**Определение:** Multithreading - это способ использования нескольких потоков внутри одного процесса для ускорения выполнения программы.

**Пример:**
```python
import threading

def print_numbers():
    for i in range(5):
        print(i)

def print_letters():
    for letter in 'ABCDE':
        print(letter)

thread1 = threading.Thread(target=print_numbers)
thread2 = threading.Thread(target=print_letters)

thread1.start()
thread2.start()

thread1.join()
thread2.join()
```

## GIL (Global Interpreter Lock)

**Определение:** GIL - это механизм в CPython (стандартная реализация Python), который гарантирует, что только один поток Python выполняет байт-код в любой конкретный момент времени.

**Зачем нужен GIL:**
1. Облегчение реализации интерпретатора.
2. Предотвращение конфликтов при изменении объектов в памяти.

**Проблемы GIL:**
1. **Ограничивает производительность многозадачных приложений:** Из-за GIL выполнение многозадачных приложений в Python может замедляться.
2. **Не дает реального многозадачного выполнения:** Потоки не могут эффективно использовать многозадачность на многоядерных системах.

**Обход GIL в Python:**
1. **Использование multiprocessing вместо multithreading:** Использование нескольких процессов вместо потоков для обхода GIL.
2. **Использование Jython или IronPython:** В этих реализациях Python GIL отсутствует.

**Пример обхода GIL с использованием multiprocessing:**
```python
from multiprocessing import Pool

def foo(n):
    return n * n

if __name__ == '__main__':
    with Pool(4) as p:
        result = p.map(foo, [1, 2, 3, 4])
    print(result)
```
