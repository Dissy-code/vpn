# vpn


hysteria2://Linuxasdf12jngb@vpn.dissyserv.ru:443/?insecure=1&sni=web.max.ru#1


hysteria2://Linuxasdf12jngb@strayfi.ru:443?security=tls&insecure=1&sni=web.max.ru#dissy


import socket

# Настройки сервера
HOST = '127.0.0.1'  # Локальный адрес
PORT = 65432        # Порт (можно поменять)

def start_server():
    # Создаем TCP сокет
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, PORT))
        s.listen()
        print(f"Сервер запущен на {HOST}:{PORT}. Ожидание данных...")

        while True:
            conn, addr = s.accept()
            with conn:
                print(f"Подключено: {addr}")
                # Ждем 9 байт данных (3 символа * 3 координаты)
                data = conn.recv(9)
                
                if not data:
                    break
                
                # Декодируем байты в строку
                msg = data.decode('utf-8')
                
                if len(msg) == 9:
                    # Нарезаем строку на координаты
                    x = msg[0:3]
                    y = msg[3:6]
                    z = msg[6:9]
                    
                    print(f"Получены координаты:")
                    print(f"X: {x} | Y: {y} | Z: {z}")
                    
                    # Отправляем подтверждение клиенту
                    conn.sendall(b"OK")
                else:
                    print(f"Ошибка: получено {len(msg)} символов вместо 9")
                    conn.sendall(b"ERROR: Need 9 chars")

if __name__ == "__main__":
    start_server()











    import socket

# Настройки сервера
HOST = '127.0.0.1'
PORT = 65432

def start_server():
    # Создаем TCP сокет
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            # Разрешаем повторное использование порта (чтобы не ждать после перезапуска)
            s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
            s.bind((HOST, PORT))
            s.listen()
            print(f"Сервер запущен на {HOST}:{PORT}")
            print("Ожидание данных (9 символов)... Нажмите Ctrl+C для выхода.")

            while True:
                conn, addr = s.accept()
                with conn:
                    # Устанавливаем таймаут, чтобы сокет не висел вечно
                    conn.settimeout(5.0)
                    try:
                        data = conn.recv(9)
                        if not data:
                            continue

                        msg = data.decode('utf-8').strip()
                        
                        # Проверяем длину (учитываем, что может прийти меньше)
                        if len(msg) == 9:
                            x, y, z = msg[0:3], msg[3:6], msg[6:9]
                            print(f"[{addr}] Получено -> X: {x}, Y: {y}, Z: {z}")
                            conn.sendall(b"OK")
                        else:
                            print(f"[{addr}] Неверный формат: {msg}")
                            conn.sendall(b"ERROR: Need 9 chars")
                    except Exception as e:
                        print(f"Ошибка при обработке данных: {e}")
    except Exception as e:
        print(f"Не удалось запустить сервер: {e}")

if __name__ == "__main__":
    start_server()
