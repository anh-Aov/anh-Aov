
#include <SFML/Graphics.hpp>
#include <SFML/Window.hpp>
#include <SFML/System.hpp>
#include <vector>
#include <cstdlib>
#include <ctime>

class Bird {
public:
    sf::CircleShape shape;
    float gravity = 0.5f;
    float velocity = 0.0f;

    Bird() {
        shape.setRadius(15);
        shape.setFillColor(sf::Color::Yellow);
        shape.setPosition(100, 300);
    }

    void update() {
        velocity += gravity;
        shape.move(0, velocity);
        
        // Dừng lại khi chạm đáy
        if (shape.getPosition().y >= 600 - shape.getRadius() * 2) {
            shape.setPosition(100, 600 - shape.getRadius() * 2);
            velocity = 0;
        }
    }

    void flap() {
        velocity = -10.0f; // Thay đổi vận tốc khi nhảy
    }
};

class Pipe {
public:
    sf::RectangleShape top;
    sf::RectangleShape bottom;
    float speed = 5.0f;

    Pipe(float x) {
        top.setSize(sf::Vector2f(50, rand() % 300));
        top.setFillColor(sf::Color::Green);
        top.setPosition(x, 0);
        
        bottom.setSize(sf::Vector2f(50, 600 - top.getSize().y - 150));
        bottom.setFillColor(sf::Color::Green);
        bottom.setPosition(x, 600 - bottom.getSize().y);
    }

    void update() {
        top.move(-speed, 0);
        bottom.move(-speed, 0);
    }
};

int main() {
    srand(static_cast<unsigned>(time(0)));
    sf::RenderWindow window(sf::VideoMode(800, 600), "Flappy Bird");
    Bird bird;
    std::vector<Pipe> pipes;
    float pipeSpawnTimer = 0;

    while (window.isOpen()) {
        sf::Event event;
        while (window.pollEvent(event)) {
            if (event.type == sf::Event::Closed)
                window.close();
        }

        if (sf::Keyboard::isKeyPressed(sf::Keyboard::Space)) {
            bird.flap();
        }

        bird.update();

        // Tạo ống mới mỗi 100 khung hình
        pipeSpawnTimer++;
        if (pipeSpawnTimer >= 100) {
            pipes.emplace_back(800);
            pipeSpawnTimer = 0;
        }

        for (auto& pipe : pipes) {
            pipe.update();
        }

        // Xóa ống đã ra khỏi màn hình
        if (!pipes.empty() && pipes.front().top.getPosition().x < -50) {
            pipes.erase(pipes.begin());
        }

        window.clear();
        window.draw(bird.shape);
        for (auto& pipe : pipes) {
            window.draw(pipe.top);
            window.draw(pipe.bottom);
        }
        window.display();
    }

    return 0;
}

