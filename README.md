# Python으로 YouTube 비디오 다운로드

이 프로젝트는 `pytubefix`를 사용하여 YouTube 비디오를 다운로드하고, 원하는 화질로 비디오와 오디오를 결합하는 스크립트를 제공합니다.

## 목차
- [설치](#설치)
- [사용법](#사용법)
- [코드 예시](#코드-예시)

## 설치

1. Python과 pip가 설치되어 있어야 합니다. 확인하려면 아래 명령어를 입력하세요.
    ```bash
    python --version
    pip --version
    ```

2. `pytubefix`와 `ffmpeg`를 설치합니다.
    ```bash
    pip install pytubefix
    ```

3. **FFmpeg 설치**
    - **Ubuntu**:
        ```bash
        sudo apt update
        sudo apt install ffmpeg
        ```

    - **Windows**: [FFmpeg 공식 사이트](https://ffmpeg.org/download.html)에서 다운로드하고 설치합니다. 시스템 환경 변수에 추가하여 `ffmpeg` 명령어를 사용할 수 있게 설정합니다.

## 사용법

1. 위 코드를 복사하여 `.py` 파일로 저장합니다. (예: `youtube_downloader.py`)
2. `video_url`에 다운로드할 YouTube 비디오의 URL을 입력합니다.
3. 원하는 화질을 입력할 수 있습니다.
4. 스크립트를 실행하여 비디오를 다운로드합니다.

## 코드 예시

아래는 다운로드 및 결합 기능을 포함한 코드입니다.

```python
from pytubefix import YouTube
import os

# 유튜브 링크 입력
video_url = 'https://www.youtube.com/watch?v=영상_ID'

# YouTube 객체 생성
yt = YouTube(video_url)

# 사용 가능한 비디오 해상도 목록 표시
print("사용 가능한 비디오 해상도:")
available_resolutions = yt.streams.filter(adaptive=True, file_extension='mp4', only_video=True).order_by('resolution').desc()
for stream in available_resolutions:
    print(f"- {stream.resolution}")

# 원하는 해상도 입력
selected_resolution = input("원하는 해상도를 입력하세요 (예: 720p): ")

# 선택한 해상도의 비디오 스트림 선택
video_stream = available_resolutions.filter(resolution=selected_resolution).first()

# 오디오 스트림 선택
audio_stream = yt.streams.filter(only_audio=True, file_extension='mp4').first()

# 파일 저장 경로 설정
output_path = '저장_폴더_경로'
video_filename = 'video.mp4'
audio_filename = 'audio.mp4'

# 비디오 다운로드
if video_stream:
    video_stream.download(output_path=output_path, filename=video_filename)
else:
    print(f"{selected_resolution} 해상도의 비디오를 찾을 수 없습니다.")

# 오디오 다운로드
audio_stream.download(output_path=output_path, filename=audio_filename)

# 비디오와 오디오 결합
output_filename = 'output_video.mp4'
os.system(f'ffmpeg -i {os.path.join(output_path, video_filename)} -i {os.path.join(output_path, audio_filename)} -c:v copy -c:a aac -strict experimental {os.path.join(output_path, output_filename)}')

# 원본 비디오와 오디오 파일 삭제
os.remove(os.path.join(output_path, video_filename))
os.remove(os.path.join(output_path, audio_filename))

print("다운로드 및 결합 완료! 원본 파일이 삭제되었습니다.")
