# Custom Dataset으로 YOLOv4 학습시키기 (Google Colab)

## 사전 준비

* Github repository 다운로드 :
	* Code - Download ZIP 클릭하여 다운로드 - 압축해제 - Google Drive에 업로드 또는
	* 커맨드 명령어 입력하여 다운로드 - Google Drive에 업로드
```bash
git clone https://github.com/prierKT/yolov4-training-colab.git
```

* Source Code 열기 : 업로드한 폴더에서 Yolov4_Training.ipynb 파일 열기

* Google Colab GPU 설정 : 상단의 Runtime 메뉴 - Change runtime type - Hardware accelerator 를 None에서 GPU로 변경 - Save

* Colab 연결 : 우측 상단의 Connect 클릭

## 코드 설명
Yolov4_Training.ipynb 파일의 코드들을 설명하는 내용입니다. 본인에게 맞게 경로만 바꿔주시면 문제 없이 실행되는 것을 확인하였습니다. 학습이 진행되지 않으시는 분들은 경로가 맞지 않거나 Dataset에 문제가 있는 것이므로 다시 한 번 확인하시고 실행해 주시기 바랍니다.

### 사전 학습 YOLOv4 테스트

* 코드 실행 : Ctrl + F9 를 누르면 모든 코드가 자동으로 실행 (중간에 assert False 코드가 있기 때문에 도중에 멈추게 되어있습니다.)
	* Darknet을 cloning하여 Colab의 가상환경에 Building
	* 사전 학습된 Yolov4 가중치를 다운로드
	* 앞으로 쓰일 함수 정의
	* 위에서 Download한 Yolov4가 잘 Building이 되었는지 테스트
	* 코드 중지 (assert False)

*Custom dataset으로 학습시키는 과정만 생각하면 위의 코드는 필요없지만 나중에 생기는 문제를 막기위해 제대로 Building이 되었는지 테스트하는 것을 추천합니다.*

### 학습 환경 설정하기

* 나의 구글 드라이브와 Colab을 연동

* 현재 디렉토리를 가상환경에 building한 darknet으로 변경 (중요)

* custom dataset setting : 학습시킬 이미지 파일들과 각각의 이미지 파일에 대한 yolo형식에 맞게 변환된 labeling 파일이 사전에 만들어져 있어야 합니다.
	* train용 폴더에, 학습시킬 이미지들과 label 파일들을 한 폴더 안에 넣고 폴더 이름을 obj로 변경하여 .zip으로 압축 (obj.zip)
	* test용 폴더에, 검증할 이미지들과 label 파일들을 한 폴더 안에 넣고 폴더 이름을 test로 변경하여 .zip으로 압축 (test.zip)
	* 압축한 두 폴더를 구글드라이브에 업로드
	* colab에서 올바른 경로에 잘 업로드 했는지 확인
	* colab 가상환경에 dataset 복사 및 압축풀기


### Configuration file 설정 
* building한 darknet폴더에서 나의 드라이브로 yolov4-custom.cfg파일 복사
* 나의 드라이브의 yolov4폴더의 yolov4-custom.cfg 더블클릭 - 연결 앱 - Text Editor
#### Training Hyper Parameter Setting
* batch = 64
* subdivisions = 16,
* width, height = 416 or 608<br>
(608로 진행하면 정확도는 더 좋으나 시간이 오래걸리고, 컴퓨터 메모리가 부족할 수 있음)
* max_batches = 내가 학습시킬 classes 개수 * 2000<br>
[예시: Coffee_cup, Flower, Table 일 경우 3 * 2000 = 6000. 단, 클래스가 1개 2개인 경우에도 최소 6000으로 유지할 것.
* steps = ( 0.8 * max_batches, 0.9 * max_batches )
* classes = 내가 학습시킬 클래스 수<br>
Ctrl + f 로 `classes`를 검색하여 내가 학습시킬 클래스 수로 바꿔준다. (수정할 부분 : 총 3개)
* filters= ( 내가 학습시킬 classes 수 + 5 ) * 3<br>
[예시: Coffee_cup, Flower, Table 일 경우 (3 + 5) * 3 = 24<br>
*classes를 찾았을때 그 바로 위에 나오는 filters만 수정(총 3개) 다른 filters는 건드리지 마세요*
* 저장 : 상단의 Save to Drive 또는 Ctrl + S
* 수정한 .cfg 파일을 다시 darknet에 덮어쓰기

### 학습 경로 설정 및 가중치 백업 경로 설정
#### obj.names file setting
로컬컴퓨터에서 obj.names 파일을 만들고 메모장으로 열어서 내가 학습시킬 class name을 한줄에 하나씩 입력 후 저장
* 저장 후 구글드라이브 - yolov4-training-colab폴더 안에 업로드

#### obj.data file setting
* 구글드라이브 - yolov4-training-colab폴더 안에 backup 폴더를 새로 생성
* 로컬컴퓨터에서 obj.data 파일을 만들고 메모장으로 열어서 :
	* classes = 내가 학습시킬 classes 개수
	* train = data/train.txt
	* valid = data/test.txt
	* names = data/obj.names
	* backup = 방금전에 만든 구글드라이브의 backup폴더의 경로 입력 후 저장
* 저장 후 구글드라이브 - yolov4-training-colab폴더 안에 업로드

* obj.names, obj.data 파일을 가상환경의 darknet/data폴더로 복사

* 구글 드라이브의 yolov4폴더안의 generate_train.py, generate_test.py폴더를 darknet 폴더로 복사 및 실행

* 사전학습된 yolov4에 building된 레이어를 다운로드

### 학습

* 학습 시작

* 결과 확인 및 테스트