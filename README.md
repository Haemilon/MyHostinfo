# MyHostinfo

작은 화면에 **집이나 사무실 서버들의 상태**를 번갈아 보여 주는 모니터입니다. 서버 이름·IP·CPU·RAM·디스크·GPU, Docker 컨테이너와 Proxmox VM 목록, 날씨·시계를 보여 줍니다.

![실제 SmallTV — 날씨·시계 화면](docs/images/smalltv-photo.jpg)

![SmallTV 서버 모니터 — 서버 정보, Docker 목록, 날씨 화면](docs/images/smalltv-hero.png)

## 이 포크에서 바뀐 점 (Proxmox 한 대 모니터링용)

원본 [saintsc-ai/MyHostinfo](https://github.com/saintsc-ai/MyHostinfo)(`3cb23e1`)에서 **Proxmox 서버 하나를 SmallTV로 늘 보는 용도**에 맞춰 고쳤습니다.

| 항목 | 원본 | 이 포크 |
|---|---|---|
| Proxmox 화면 | 서버 정보 8초 → VM 목록 6초 → 날씨 5초 번갈아 | **PVE 한 화면만 계속**(끊기면 날씨·시계 전체 화면으로 바뀌어 장애가 보임) |
| 제목줄 | 서버 이름 | 서버 이름 + 오른쪽 `up 2d 21h`(흰색) |
| VM 줄 | `VM 1/2 run`(QEMU 만) | `CT 200 201 VM 102` 번호를 상태 색으로(초록 실행·노랑 멈춤·회색 꺼짐), 한 줄에 안 들어가면 `CT 2/2 VM 1/1` |
| 아래 칸 | 큰 시계 | 12시간 시계 `6:52` + `PM 10-31`, 날씨 아이콘(32px)·기온·습도·최저~최고 |
| 온도 표시 | `45C` | `45°C`(모든 °C 같은 가는 원 모양) |
| 갱신 | 바뀐 줄을 지우고 다시 그려 깜빡임 | 바탕째 덮어 그려 숫자만 바뀜 |
| 서버 부하 | `qm list`(약 0.8초 CPU)를 3초마다 | `pvesh` 결과를 15초 캐시해 함께 씀(`qm list` 없음) |

- Proxmox 가 아닌 서버(Docker 등)는 원래대로 서버 정보 → 목록 → 날씨를 번갈아 보입니다. 화면 시간은 서버 정보 40초 · 목록 18초 · 날씨 20초입니다.
- 서버 프로그램은 HOST 줄에 게스트 번호 필드 `&q102g,l200g`(q=VM, l=LXC, 상태 g·y·o)를 더 보내고, Proxmox 목록(LIST)은 보내지 않습니다. ESP32-S3 펌웨어 소스와 PC 창 앱은 이 필드를 건너뛰게 고쳤습니다.
- **펌웨어와 서버 프로그램을 함께 바꿔야** 위 화면이 됩니다.
- `bin/SDP-hostinfo-smalltv.bin`·`bin/nodemcu-hostinfo.bin` 은 이 포크 소스로 다시 빌드했습니다(ESP8266 Arduino 코어 3.1.2, 4M1M — PlatformIO `espressif8266@4.2.1` 과 같은 코어·플래시 배치라 설정이 그대로 남습니다). **`bin/esp32s3-*.bin` 은 다시 빌드하지 않았습니다** — 이 포크 서버와 쓰면 `&…` 줄이 화면에 그대로 보이니, ESP32-S3 는 `firmware/esp32s3` 를 PlatformIO 로 다시 빌드해 쓰세요.
- 이미 이 펌웨어가 든 SmallTV 는 `python espota.py -i <SmallTV 주소> -p 8266 -a "<OTA 비밀번호>" -f bin/SDP-hostinfo-smalltv.bin` 으로 올립니다.

## 세 가지 화면

| | GeekMagic SmallTV | ESP32-S3 + ST7789 화면 | NodeMCU + ST7789 화면 |
|---|---|---|---|
| 모습 | 케이스가 있는 완제품 날씨 시계 | 개발 보드에 화면 모듈을 점퍼선으로 연결 | 개발 보드에 화면 모듈을 점퍼선으로 연결 |
| 설치 | 원래 펌웨어의 웹 업데이트로 Wi-Fi만으로 올림. 납땜·케이블 없음 | USB로 한 번 구움(브라우저로 가능) | USB로 한 번 구움(브라우저로 가능) |
| 설정 | 휴대폰으로 설정 페이지 | USB 시리얼 명령 | USB 시리얼 명령 |
| 특징 | 가장 간단함 | Wi-Fi 화면 | **USB로 꽂으면 그 컴퓨터만** 보이는 USB 화면(Wi-Fi 설정 없이), Wi-Fi 화면도 됨 |
| 화면 | 1.54" 240×240 | 1.54"·1.69"·1.14"·2.0"·2.25" | 1.54"·1.69"·1.14"·2.0"·2.25" |
| 설치 방법 | **[docs/smalltv.md](docs/smalltv.md)** | **[docs/esp32s3.md](docs/esp32s3.md)** | **[docs/nodemcu.md](docs/nodemcu.md)** |

**화면 보드 없이 PC 창으로도** 볼 수 있습니다 → **[docs/desktop.md](docs/desktop.md)**(240×240 창, 화면 보드와 같은 그림, 파이썬만 있으면 됨). **하드웨어가 없어도 서버가 정보를 제대로 보내는지 이 창으로 확인할 수 있습니다.**

어느 경우든 상태를 보여 줄 컴퓨터마다 서버 프로그램을 설치합니다 → **[docs/server.md](docs/server.md)**(리눅스·Windows·맥).

![화면 모음](docs/images/smalltv-screens.png)

## 동작 방식

![동작 방식 — 서버가 UDP 4210으로 상태를 보내고, 화면이 UDP 4211로 "여기 있음"을 알리면 서버가 그 화면에 직접도 보냅니다](docs/images/flow-groups.png)

- 서버 프로그램 [`server/hostinfo_serial.py`](server/hostinfo_serial.py)는 파이썬 3 표준 라이브러리만 씁니다. 리눅스에서는 Docker 컨테이너(`docker ps`·`docker stats`)와 Proxmox VM(`pvesh`), `nvidia-smi`가 있으면 GPU도 보냅니다.
- 같은 LAN이면 브로드캐스트로 서로 찾습니다. Wi-Fi로 브로드캐스트가 넘어가지 않는 곳(기업용 무선 컨트롤러, AP 격리)에서는 화면 설정에 서버 주소를 적습니다.
- 날씨는 [Open-Meteo](https://open-meteo.com/)에서 받습니다(API 키 없음). 시계는 한국 시간(KST)입니다.

## 저장소 구성

| 경로 | 내용 |
|---|---|
| [`bin/`](bin/) | 미리 빌드한 펌웨어(`SDP-hostinfo-smalltv.bin`, `esp32s3-hostinfo-full.bin`, `esp32s3-hostinfo-ota.bin`, `nodemcu-hostinfo.bin`) |
| [`server/`](server/) | 서버 프로그램과 설치 스크립트(systemd·작업 스케줄러·launchd) |
| [`firmware/esp8266/`](firmware/esp8266/) | SmallTV(ESP8266) 펌웨어 소스. NodeMCU + 화면 모듈(`nodemcuv2`)도 빌드됩니다 |
| [`firmware/esp32s3/`](firmware/esp32s3/) | ESP32-S3 펌웨어 소스 |
| [`docs/`](docs/) | 설치 문서와 그림 |
| [`desktop/`](desktop/) | PC 창 앱(`hostinfo_window.py`, 파이썬 tkinter) |
| [`tools/`](tools/) | 펌웨어 빌드(`build-bin.sh`), 화면 미리보기 그림(`preview.py`, `make-hero.py`) |

## 소스로 빌드하기

[PlatformIO](https://platformio.org/)가 필요합니다.

```sh
cd firmware/esp8266 && pio run               # SmallTV(smalltv)·NodeMCU(nodemcuv2)
cd firmware/esp32s3 && pio run                # ESP32-S3
sh tools/build-bin.sh                         # 둘 다 빌드해 bin/ 에(esptool 필요)
```

## 라이선스

[MIT](LICENSE). 펌웨어는 Arduino 코어와 Adafruit GFX·ST7789 라이브러리(BSD)를 씁니다.

펌웨어를 바꾸면 제조사 보증을 받지 못할 수 있습니다. 각자 판단해서 진행하세요.
