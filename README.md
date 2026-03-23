# vpn


hysteria2://Linuxasdf12jngb@vpn.dissyserv.ru:443/?insecure=1&sni=web.max.ru#1



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
