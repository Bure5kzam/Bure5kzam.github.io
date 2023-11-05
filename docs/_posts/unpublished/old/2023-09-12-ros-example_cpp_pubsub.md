---
layout: post
title: ROS 퍼블리셔 예시 분석하기
date: 2023-09-12 20:01
category: ros
author: Bure5kzam
tags: []
summary: 개발환경 설정하기
---

## Publisher

``` c++
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"
using namespace std::chrono_literals;

class CustomPublisher : public rclcpp::Node {
  public:
    CustomPublisher():Node("custom_publisher"), count_(0) {
      publisher_ = this->create_publisher<std_msgs::msg::String>("topic", 10);
      timer_ = this->create_wall_timer(500ms, std::bind(&CustomPublisher::timer_callback, this));
    }

  private:
    void timer_callback() {
      //callback
      auto msg = std_msgs::msg::String();
      msg.data = "Hello, world" + std::to_string(count_++);
      RCLCPP_INFO(this->get_logger(), "Publishing : %s", msg.data.c_str());
      publisher_->publish(msg);
    }

    size_t count_;
    rclcpp::TimerBase::SharedPtr timer_;
    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
};

int main(int argc, char* argv[])
{
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<CustomPublisher>());
  rclcpp::shutdown();
}

```

### 헤더

```c++
#include "rclcpp/rclcpp.hpp"
/*
 ros 라이브러리를 포함하는 구문.
프로세스 단위가 되는 노드를 포함해 내부적으로 노드 구성에 필요한 퍼블리셔, 섭스크라이버를 생성하는 메소드를 제공한다.
*/

#include "std_msgs/msg/string.hpp"
/*
ros 에서 사용할 메세지 클래스를 포함하는 구문.
단순한 스트링 메세지를 송/수신할 때 사용
*/

using namespace std::chrono_literals;

/*
* chrono
  c++ 11부터 제공하는 시간 관련 자료형
  std::chrono::second, std::chrono::chrono::minutes 등이 있음

* using namespace std::chrono_literals
  숫자 뒤에 리터럴 접미사로 붙이면 적절한 리터럴 자료형으로 변환시킬 수 있게 해주는 문장
  ex) auto five_second = 5s;
  를하면 컴파일 동안에 std::chrono::second 자료형의 five_second에 5가 할당됨.
*/

```

### 노드

```c++
class CustomPublisher : public rclcpp::Node {
  /*
  rclcpp의 노드 클래스인 Node를 상속
  노드는 프로세스 수행 단위가 되며 라이프사이클을 가짐.
  */

  public:
    CustomPublisher():Node("custom_publisher"), count_(0) {
    /*
    먼저 Node 생성자를 호출하여 노드 이름을 custom_publisher로 지정.
    이어서 클래스 필드 count_에 0을 할당
    그 후에 생성자를 실행
    */

      publisher_ = this->create_publisher<std_msgs::msg::String>("topic", 10);
      /*
      노드에서 사용할 퍼플리셔를 생성하려면 Node 클래스의 create_publisher 메서드를 호출.
      예시에서는 발행할 토픽 명과 qos를 아규먼트로 줌.
      */

      timer_ = this->create_wall_timer(500ms, std::bind(&CustomPublisher::timer_callback, this));
      /*
      퍼블리셔 노드에서 사용할 타이머를 사용하려면 Node 클래스의 create_wall_timer를 호출.

      * std::bind
        c++에서 콜백 함수로 등록하려면 함수 객체 또는 함수 포인터가 필요함.
        하지만 함수 포인터나 함수 객체로 나타낼 수 있는 일반/전역 함수와 달리 맴버 함수는 클래스 인스턴스가 있어야 호출할 수 있음.
      */
    }

  private:
    void timer_callback() {
      //callback

      auto msg = std_msgs::msg::String();
      msg.data = "Hello, world" + std::to_string(count_++);
      RCLCPP_INFO(this->get_logger(), "Publishing : %s", msg.data.c_str());
      /*
      로그를 사용해 문자열을 출력하기 위한 매크로문. (메서드가 아님)
      */
      publisher_->publish(msg);
      /*
      주기적으로 메세지를 발행하는 퍼블리시 함수를 실행.
      메세지는 Hello, world인데 왭 출력 결과가 아래와 같은지는 모르겟음.
      [INFO] [1694681414.730776218] [custom_publisher]: Publishing : Hello, world0
      [INFO] [1694681415.230741182] [custom_publisher]: Publishing : Hello, world1
      [INFO] [1694681415.730792835] [custom_publisher]: Publishing : Hello, world2
      */
    }

    size_t count_;
    rclcpp::TimerBase::SharedPtr timer_;
    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
    /*
    * SharedPtr
    포인터를 참조하는 변수가 없으면 자동으로 해제하여 메모리 누수가 없게 지원하는 클래스
    */
};
```

### Main

```c++
int main(int argc, char* argv[])
{
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<CustomPublisher>());
  /*
  * make_shared
  SharedPtr 자료형 객체를 생성하는 매서드. '<>' 안의 템플릿으로 지정된 자료형을 가짐
  https://en.cppreference.com/w/cpp/memory/shared_ptr/make_shared
  */
  rclcpp::shutdown();
}
```

## Subscriber

```c++
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"
using std::placeholders::_1;

class MinimalSubscriber : public rclcpp::Node
{
  public:
    MinimalSubscriber()
    : Node("minimal_subscriber")
    {
      subscription_ = this->create_subscription<std_msgs::msg::String>(
      "topic", 10, std::bind(&MinimalSubscriber::topic_callback, this, _1));
    }

  private:
    void topic_callback(const std_msgs::msg::String & msg) const
    {
      RCLCPP_INFO(this->get_logger(), "I heard: '%s'", msg.data.c_str());
    }
    rclcpp::Subscription<std_msgs::msg::String>::SharedPtr subscription_;
};

int main(int argc, char * argv[])
{
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<MinimalSubscriber>());
  rclcpp::shutdown();
  return 0;
}
```

