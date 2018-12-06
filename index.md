# Computed Photography Assignment 1

### Initials
-------------
Post-processing을 소수점 단위까지 할 수 있도록 double 형으로 선언하였다.

(a)
<pre><code>I = imread('banana_slug.tiff');
size(I)
</code></pre>

(b) 2856×4290 uint16
(c) 
<pre><code> I = double(I);
</code></pre>

### Linearization
-------------
Pixel 값이 15000보다 큰 값은 카메라 과노출로 인한 잘못된 값이고, Pixel 값이 2047보다 작은 값은 Black을 의미하기 때문에 다음과 같이 Linearization을 진행하였다.

<pre><code>I = (I-2047)./(15000-2047);
I(find(I > 1)) = 1;
I(find(I < 0)) = 0;           
</code></pre>

![image](https://user-images.githubusercontent.com/45420635/49576363-58791980-f988-11e8-82c0-a9f19817654f.png)

### Identifying the Correct Bayer Pattern
-------------
![image](https://user-images.githubusercontent.com/45420635/49576386-6890f900-f988-11e8-8089-649e0ad2ea0b.png)

4가지 케이스에 대하여 결과그림은 다음과 같다. 이 중에서 주어진 'Reference Image'와 대조해 보았을 때, 옷 색깔의 흰 줄무늬와 파란 줄무늬가 비교적 잘 구분되는 'rggb'와 'bggr'이 유력하였고, 두 이미지를 비교했을 때 'Reference Image' 속의 'Yellow slug'가 'rggb'에서 더 그럴듯한 색상으로 표현됨을 확인하였다. 따라서 해당 카메라의 Bayer Pattern은 'rggb'임을 유추하였다.

(a) 'grbg'
![image](https://user-images.githubusercontent.com/45420635/49576406-78104200-f988-11e8-8d07-026fed6792bb.png)

(b) 'rggb'
![image](https://user-images.githubusercontent.com/45420635/49576425-84949a80-f988-11e8-9a66-e046b0be00ac.png)

(c) 'bggr'
![image](https://user-images.githubusercontent.com/45420635/49576446-8d856c00-f988-11e8-9693-978b6d3da679.png)

(d) 'gbrg'
![image](https://user-images.githubusercontent.com/45420635/49576463-97a76a80-f988-11e8-8e80-6c6f271ce33a.png)

### White Balancing
-------------
Grey world assumption과 White world assumption으로 White Balancing을 해본 결과, White world assumption에서 원본 이미지와의 유사점에서 조금 더 자연스러운 결과가 나왔다. 
그 근거로는, 사진 속 사람의 피부가 Grey world assumption에서는 약간 창백한 빛을 띄었으나, White world assumption에서는 좀 더 자연스러운 누런 피부색으로 표현이 되었고 'Reference Image' 속 'Yellow slug'가 White world에서 더 Yellow에 가까운 색상을 나타내었다. 사진 속 여자의 금발 머리도 White world에서 더 잘 표현된 것을 볼 수 있다. 
그러나 아직, Demosaicing과 Gamma Correction 단계를 거치지 않았기에 Grey world assumption과 White world assumption에 대해서는 모든 post-processing 과정을 거친 후에 다시 논의하겠다.

(a) Grey World Assumption

![image](https://user-images.githubusercontent.com/45420635/49576495-ae4dc180-f988-11e8-90b1-250bf56af56f.png)

![image](https://user-images.githubusercontent.com/45420635/49576502-b443a280-f988-11e8-98ee-fb070a7fba63.png)

(b) White World Assumption

![image](https://user-images.githubusercontent.com/45420635/49576615-f53bb700-f988-11e8-80fe-2cc1e942c1bc.png)

![image](https://user-images.githubusercontent.com/45420635/49576630-fbca2e80-f988-11e8-95ca-06b4c049b614.png)

### Demosaicing
-------------
추출한 각각의 RGB Matrix에서 interp2함수를 사용하면 원본 픽셀의 개수보다 상하, 좌우 하나씩 픽셀이 줄어들게 된다. 이 문제를 해결하기 위해 paddarray함수를 사용하여 RGB Matrix에 같은 값으로 한 픽셀씩 padding을 한 뒤 Bilinear interpolation을 하고 복제된 값을 삭제해주었다. 이후 Correct Bayer Pattern 과정을 통하여 원본 이미지 사이즈 (2856×4290×3) 로 복원할 수 있었다.

(a) Grey World Assumption
![image](https://user-images.githubusercontent.com/45420635/49576679-12708580-f989-11e8-9ec4-9a180f734a41.png)

(b) White World Assumption
![image](https://user-images.githubusercontent.com/45420635/49576700-1c928400-f989-11e8-9cfc-d54f20610c9b.png)

### Brightness Adjustment and Gamma Correction
-------------
(a) Grey World Assumption
Brightness Adjustment가 Linear mapping이라는 조건아래서, 원래 RGB 채널에 몇 배를 하든 rgb2gray함수의 output이 1을 넘지 않으므로 scaling percentage가 127.3%를 하였을 때가 최대가 되었다. (즉 scaling percentage가 127.3%를 넘어가게 되면 Linear mapping의 조건에 부합하지 않음) 따라서, scaling percentage가 110%일 때와 120%일 때, 그리고 127.3%일 때를 비교해보았고, Linear scaling을 최대로 한 127.3%가 가장 Quality가 높다고 평가하였다.

- Scaling Percentage = 110%
![image](https://user-images.githubusercontent.com/45420635/49576792-55caf400-f989-11e8-8716-54b50d83a54a.png)

- Scaling Percentage = 120%
![image](https://user-images.githubusercontent.com/45420635/49576806-5e232f00-f989-11e8-82c7-69dff3ca5321.png)

- Scaling Percentage = 127.3%
![image](https://user-images.githubusercontent.com/45420635/49576823-6a0ef100-f989-11e8-9df5-e1d5121cdd1f.png)

Scaling percentage가 127.3%일 때, 과제에서 주어진 대로 Gamma Correction을 수행한 결과는 아래 그림과 같다.
![image](https://user-images.githubusercontent.com/45420635/49576843-772be000-f989-11e8-9d63-eee733322651.png)

(b) White World Assumption
위와 마찬가지로, Linear mapping이라는 조건아래서, 원래 RGB 채널에 몇 배를 하든 rgb2gray함수의 output이 1을 넘지 않으므로 scaling percentage가 126.54%를 하였을 때가 최대가 되었다. (즉 scaling percentage가 126.54%를 넘어가게 되면 Linear mapping의 조건에 부합하지 않음) 따라서, scaling percentage가 110%일 때와 120%일 때, 그리고 126.54%일 때를 비교해보았고, Linear scaling을 최대로 한 126.54%가 가장 Quality가 높다고 평가하였다.

- Scaling Percentage = 110%
![image](https://user-images.githubusercontent.com/45420635/49576893-975b9f00-f989-11e8-9e93-cdad74f65ce0.png)

- Scaling Percentage = 120%
![image](https://user-images.githubusercontent.com/45420635/49576900-9e82ad00-f989-11e8-94ad-23e893e55608.png)

- Scaling Percentage = 127.3%
![image](https://user-images.githubusercontent.com/45420635/49576911-a5112480-f989-11e8-87fb-f88fedee59a6.png)

Scaling percentage가 127.3%일 때, 과제에서 주어진 대로 Gamma Correction을 수행한 결과는 아래 그림과 같다.
![image](https://user-images.githubusercontent.com/45420635/49576928-ae01f600-f989-11e8-8d2c-f9ea3b0aaa91.png)

### Compression
-------------
(a) PNG format 의 file size = 13.9MB이고, JPEG format의 file size = 2.50MB이므로 육안으로는 사진의 Quality에 차이를 볼 수 없지만 용량 크기의 차이가 5배 이상 난다.

(b) Compression Ratio =(2×2,856×4,290 bytes)/(2,630,489 bytes)=(24,504,480 bytes)/(2,630,489 bytes)=9,316

- JPEG Quality가 95일 때
![image](https://user-images.githubusercontent.com/45420635/49577015-dab60d80-f989-11e8-8943-87f9a2986b88.png)

- JPEG Quality가 50일 때
![image](https://user-images.githubusercontent.com/45420635/49577051-ee617400-f989-11e8-892b-1cf8a6b95a81.png)

- JPEG Quality가 30일 때
![image](https://user-images.githubusercontent.com/45420635/49577062-f5888200-f989-11e8-9e33-f7babffc3161.png)

- JPEG Quality가 15일 때
![image](https://user-images.githubusercontent.com/45420635/49577069-fb7e6300-f989-11e8-823f-be3cdee2411c.png)

JPEG Quality를 낮춰가며 실험해본 결과, 관용적으로, Quality가 50일 때까지는 어느 정도 원본과 유사하다고 말할 수 있었다. 이 때, Compression Ratio = (2×2,856×4,290 bytes)/(626,791 bytes)=(24,504,480 bytes)/(626,791 bytes)=39.10이다.
