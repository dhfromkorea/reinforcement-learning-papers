# Paper

* **Title**: A Machine Learning Approach to Visual Perception
of Forest Trails for Mobile Robots
* **Authors**: Alessandro Giusti et al
* **Link**: http://rpg.ifi.uzh.ch/docs/RAL16_Giusti.pdf
* **Tags**: Visual-Based Navigation; Aerial Robotics; Machine Learning; Deep Learning
* **Year**: 2015

## TL;DR
* 이 [데모 비디오](http://people.idsia.ch/~giusti/forest/web/) 가 잘 요약해주었습니다.

## 요약
* 어린 아이가 혼자 산을 넘으려고 한다. 이때 해줄 수 있는 좋은 조언은 "길만 잘 따라다녀"일 것입니다. 
* MAV(드론처럼 날아다니는 조그만 로봇들)을 잘 학습시켜 안전하게 비행 할 수 있게 하고 싶다고 해봅시다. 위와 마찬가지로 로봇이 길(trails)을 잘 따라가도록 해주면 해결되지 않을까요? 
* 기존의 연구자들은 길따라 문제를 segmentation으로 접근하였답니다. 길을 따라가기 위해서는 길이 어떻게 생겼고 어디에 위치한지 "길의 경계"를 찾아보자라는 접근입니다. 보통 색깔(hue) 기반으로 만든 image saliency 등 전문지식(domain knowledge)이 가미된 feature를 직접 만들어 모델을 학습시켰다고 합니다. 포장길에서는 어느정도 잘 배웠지만, 숲길, 사막길 등 variance가 큰 환경에서는 신통치 않았다고 합니다.
* 저자들은 길따라 문제를 classification problem으로 접근했습니다. 길이 포함된 input image가 주어졌을때, 현재 이동방향을 기준으로 직진할 지(GS; go straight), 좌현(TL; turn left), 우현(TR; turn right) 분류하는 문제로 생각한 것입니다. 길의 정확히 어디에 있는지 굳이 몰라도 된다. 길이 향하는 방향에만 잘 맞추어서 비행하면 장땡이라는 아이디어입니다.
* 길이 향하는 방향에 "얼마나" 잘 맞추며 비행하고 있는지는 직접적으로 알기 힘듭니다. 아타리 게임같은 환경과는 달리 reward function을 구성하기가 어렵습니다. 하지만 길의 방향에 잘 맞추며가는지 갈 때 어떻게 행동(직진, 좌현, 우현)해야 충분히 바람직한지는 알 수 있습니다. 인간이 길따라 가는 것을 보고 이를 선생님 삼아 따라하면(imitation learning) 되는 것이지요. 

![alt text][problem_formulation]

* 저자들은 좀 더 엄밀하게 이 classification 문제를 위와 같이 정의해봤습니다. 세 방향 중, 어느 방향으로 갈 것이냐 classify하는 문제는 결국 길과 나(로봇)의 이동방향(벡터)의 함수입니다.
* 길의 방향(~t 는 ground truth direction이라고 할 수 있습니다. ~t를 직접 알 수는 없기에, data collection 당시 인간의 "정면"" 카메라의 방향(optical axis) 대체합니다. 나(로봇)의 이동방향, ~v, 는 나(로봇)의 카메라의 방향 (optical axis, 나(로봇)은 카메라가 1개입니다.)입니다.
* α 는 ~v 와 ~t의 (양수/음수) 각도 차이로 정의합니다. 내가 가는 방향고 내가 가야하는 방향의 차이라고 생각하시면 될 것 같습니다. 실제로 내가 가야하는 방향(~t)이 고유하지는 않을 것이기 때문에, β라는 임의의 margin을 주어 대체로 직진해도 되는 각도 차이(alpha)를 만듭니다.
* 다음과 같이 분류합니다.
    * Turn Left (TL) if −90◦ < α < −β; 
    * Go Straight (GS) if −β ≤ α < +β;
    * Turn Right (TR) if +β ≤ α < +90◦;

* 저자들은 (input_image, correct_behavior) 데이터를 가지고 Deep Neural Network (CNN-based) 지도학습을 시킵니다. 

![alt text][data_collection]


* 저자들은 인간의 머리에 카메라 3대를 설치하고, 다양한 길을 걷게 하였습니다. 이로써 input(camera image)와 정답(ground truth)의 map이 만들어집니다. 
    * 카메라 3대 트릭: 이 문제는 sequential/temporally correlated data이기에 iid 가정이 위반됩니다. iid 가정이 있는 supervised learning을 그대로 적용하는 경우 학습이 잘 안될 가능성이 높다고 합니다. (b/c. compounding error) non-iid 문제를 해결하기위해 인위적으로 실수상황을(failure states) 만들고 이때 어떻게 이동방향을 수정해야하는지 정답을 label합니다. 저자들은 카메라 3대(왼쪽, 직진, 오른쪽)를 설치했는데요. 예를 들어 왼쪽 카메라는 길의 왼쪽 방향으로 이동하는 "실수"를 저질렀을때 오른쪽으로 꺾어야한다는 것을 알려주는 sample로 활용될 것입니다.
    * 인간이 데이터 수집 트릭: 흔히 이미지 분류를 딥러닝 모델을 통해 할 때, 많은 훈련데이터가 필요하다고 말합니다. 만약 데이터를 인간이 걸어다니면서 수집하지 않고, 로봇이 날아다니면서 (인간이 조종하는) 했다면 배터리 life 등 문제로 데이터 습득이 쉽지 않았을 것입니다. 로봇의 비행 높이나 인간의 머리 높이가 비슷하다고 가정한다면 인간이 수집한 데이터를 로봇에 학습에 적용하는 것이 무리가 없을 것입니다. 

* 저자들은 먼저 수집한 데이터를 training (17,119 frames)와 test (7,335 frames) sets로 나누었습니다. 나눌 때, 똑같은 길이 training과 test에 나오도록 하였고, 각 set 별로 TR/TL/GS class의 비율이 고르게 분포하도록 하였습니다.

* 사용된 DNN 모델은 구체적으로 다음과 같습니다. 
    - input: 3 × 101 × 101 (RGB-valued frames)
    - num_layers: 9 (CNN)
    - output: softmax probabilities for 3 classes
    - The DNN is trained using backpropagation for 90 epochs. The learning rate started at 0.005 and decayed by 5% per epoch. Weights were init
    - ialized by sampling from uniform distribution. Activations used were tanh and the final activation function used was softmax.

![alt text][cnn]

* 저자들은 테스트를 안에서 그리고 밖에서 (field test) 모두 합니다. 먼저 안에서 하는 테스트는 training set에 학습시킨 모델을 test set에 적용시킨 것입니다. 3가지 모델(benchmark)을 두고 성과를 비교합니다. 구체적인 테스크 결과는 다음과 같습니다.

![alt text][result]

* 인간과 비교할만한 "길따라" 정확도를 보였습니다. 밖에서 MAV(Parrot
ARDrone, Quadrotor)로 field test를 한 경우, 대체로 길을 잘 따라갔다고 합니다. 잘 못따라간 경우 인간마저 헷갈릴만한 길이었다는 것입니다. 실제 로봇 controller는 두가지 변수를 조정하는 형태입니다. 첫째, Yaw(방향꺾기) ~= P(TR) - P(TL) 즉 오른쪽으로 꺾을 확률이 우세하면 오르쪽으로 꺾습니다. Speed(속력) ~= P(GS) 여기서 P()는 softmax activation 결과값으로 확률로 해석할 수 있습니다.

* field test 해보니 크게 두 가지 문제도 발견되었습니다. 하나는 좁은 길에서 혹은 운신한 공간이 많지 않은 경우, 장애물에 부딪히는 경우가 생긴다고 합니다. 이는 길만 잘 따라가는 법만 학습하였고, 장애물 인식 등은 직접적으로 학습하지 않았기에 생기는 문제입니다. 다른 문제는 학습데이터는 GoPro 이미지로 고퀄이었는데, MAV에 설치된 카메라 이미지는 상대적으로 퀄리티가 떨어져서 input image가 충분한 정보를 담지 못하는 문제가 있었다고 합니다.        




[data_collection]: ../img/mobile_robot_hiker_data_acquisition.png ""
[cnn]: ../img/mobile_robot_cnn.png ""
[problem_formulation]: ../img/mobile_robot_ground_truth.png ""
[result]: ../img/mobile_robot_result.png ""