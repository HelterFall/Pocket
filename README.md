# Pocket

extends CharacterBody2D

@export var speed: float = 320.0
@export var gravity: float = 1200.0
@export var jump_strength: float = -650.0


func _ready() -> void:
    velocity.y = jump_strength


func _physics_process(delta: float) -> void:
    # Гравитация
    velocity.y += gravity * delta

    # Горизонтальное управление
    var direction := Input.get_axis("move_left", "move_right")
    velocity.x = direction * speed

    # Запоминаем, падал ли персонаж до столкновения
    var was_falling := velocity.y > 0.0

    # Физическое перемещение
    move_and_slide()

    # Прыжок только при приземлении сверху
    if was_falling:
        for i in range(get_slide_collision_count()):
            var collision := get_slide_collision(i)

            if collision.get_normal().y < -0.7:
                velocity.y = jump_strength
                break

    # Телепортация между краями экрана
    var screen_width := get_viewport_rect().size.x

    if global_position.x < -20.0:
        global_position.x = screen_width + 20.0
    elif global_position.x > screen_width + 20.0:
        global_position.x = -20.0
