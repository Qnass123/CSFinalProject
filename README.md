# CSFinalProject
# Qt pacman
# Summary
- Overview
- Itroduction
- Principal steps:
  - Step 1: declare objects and functions
  - Step 2: initialize objects
  - Step 3: Implement update_score and keyPressEvent
- Conclusion

## Introduction :

 ## Overview :
 Play with arrow keys.
 
 <p align=center>
  
  <img src="ToDoAPPs/src/image.png">
  
 </p>

 When the player winn will see:
 <p align=center>
  
  <img src="ToDoAPPs/src/image.png">
  
 </p>

 When the player lose will see:
 <p align=center>
  
  <img src="ToDoAPPs/src/image.png">
  
 </p>

## Principal steps : 
### Step 1: declare objects and functions  
- Add a `QGraphicsView` named `graphicsView` for displaying game. (In the source code, `graphicsView` is defined in [mainwindow.ui](https://github.com/blueskyson/Qt-pac-man/blob/master/mainwindow.ui).)  
- Add a `QLabel` named `score` for displaying score.  
- Add two `QLabel`s named `win_label` and `lose_label` for displaying when game is over.  
- Add a `QTimer` named `score_timer` for updating current score.  
- Declare `keyPressEvent` override function.  
- Declare `update_score` slot function.  
- Include [game.h](https://github.com/blueskyson/Qt-pac-man/blob/master/source/game.h).

```cpp
#include <QMainWindow>
#include <QLabel>
#include <QKeyEvent>
#include <QTimer>
#include "game.h"

QT_BEGIN_NAMESPACE
namespace Ui { class MainWindow; }
QT_END_NAMESPACE

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();
    void keyPressEvent(QKeyEvent*) override;

private slots:
    void update_score();

private:
    Ui::MainWindow *ui;
    QLabel *score, *win_label, *lose_label;
    QTimer *score_timer;
    Game *game;
};
```

### Step 2: initialize objects

Setup `graphicsView` in the constructor of `MainWindow`.

```cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->graphicsView->setStyleSheet("QGraphicsView {border: none;}");
    ui->graphicsView->setBackgroundBrush(Qt::black);
    ui->graphicsView->setFocusPolicy(Qt::NoFocus);
```

Set the geometry of `graphicsView` and `game`. Read game map from [map.txt](https://github.com/blueskyson/Qt-pac-man/blob/master/game_objects/map_objects/map.txt).

```cpp
    int map_height = 20, map_width = 29;            // 20x29 game map
    int x = 50, y = 50;                             // coordinate in mainwindow
    int w = (map_width * GameObject::Width);        // width pixel
    int h = (map_height * GameObject::Width);       // height pixel
    ui->graphicsView->setGeometry(x, y, w, h);
    game = new Game(x, y, map_width, map_height, ":/game_objects/map_objects/map.txt");
    ui->graphicsView->setScene(game);
```

Initialize labels and the timer.

```cpp
    score = new QLabel(this);
    win_label = new QLabel(this);
    lose_label = new QLabel(this);
    score_timer = new QTimer(this);
    // setup labels' properties here...
```

Start timer and game.

```cpp
    score_timer->start(25);
    connect(score_timer, SIGNAL(timeout()), this , SLOT(update_score()));
    game->start();
}
```

### Step 3: Implement update_score and keyPressEvent

Update score when `score_timer` ticks. If the pacman eats all points, `game->stat` will change to `Game::Win`. If a ghost catches the pacman, `game->stat` will change to `Game::Lose`. Stop timer and show `win_label` or `lose_label` when game is over.

```cpp
void MainWindow::update_score()
{
    score->setText(QString::number(game->get_score()));
    if (game->stat == Game::Win) {
        win_label->show();
        score_timer->stop();
    } else if (game->stat == Game::Lose) {
        lose_label->show();
        score_timer->stop();
    }
}
```

Detect key press events from w, a, s, d keys and make pacman move.

```cpp
void MainWindow::keyPressEvent(QKeyEvent *e)
{
    switch (e->key()) {
    case Qt::Key_W:
        game->pacman_next_direction(GameObject::Up);
        break;
    case Qt::Key_A:
        game->pacman_next_direction(GameObject::Left);
        break;
    case Qt::Key_S:
        game->pacman_next_direction(GameObject::Down);
        break;
    case Qt::Key_D:
        game->pacman_next_direction(GameObject::Right);
        break;
    }
}
```
## Conclusion

