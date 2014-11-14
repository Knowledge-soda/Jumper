import pygame
from pygame import *
import random
import math
import tiledtmxloader
import py_gjapi


class pusher:
    def __init__(self, x1, y1, image, obj, do1=None, do2=None, time=0,
        pos_do=None, anti_pos_do=None, infinity=0, proces=2):
        self.x1, self.y1 = x1, y1
        self.image = image
        self.x2, self.y2 = self.image.get_size()
        self.obj = obj
        self.do1 = do1
        self.do2 = do2
        self.time = time
        self.proces = proces
        self.pos_do = pos_do
        self.anti_pos_do = anti_pos_do
        self.infinity = infinity

    def be(self):
        self.x, self.y = pygame.mouse.get_pos()
        if self.proces == 2:
            if self.x2 + self.x1 > self.x > self.x1:
                if self.y2 + self.y1 > self.y > self.y1:
                    self.obj.change(self.pos_do)
                    if self.obj.mouse is True:
                        self.t = pygame.time.get_ticks()
                        if self.infinity < 2:
                            self.proces = 1
                        self.obj.change(self.do1)
                else:
                    self.obj.change(self.anti_pos_do)
            else:
                self.obj.change(self.anti_pos_do)
        if self.proces == 1:
            if pygame.time.get_ticks() - self.t > self.time:
                self.obj.change(self.do2)
                if self.infinity < 1:
                    self.proces = 0
                    self.obj.change(self.anti_pos_do)
                else:
                    self.recall()
        if self.proces > 0:
            self.obj.screen.blit(self.image, (self.x1, self.y1))

    def update(self, x1, y1, image, obj, do1=None, do2=None, time=0,
        pos_do=None, anti_pos_do=None, infinity=0):
        self.x1, self.y1, = x1, y1
        self.image = image
        self.x2, self.y2 = self.image.get.size()
        self.obj = obj
        self.do1 = do1
        self.do2 = do2
        self.time = time
        self.mouse = False
        self.pos_do = pos_do
        self.anti_pos_do = anti_pos_do
        self.infinity = infinity

    def recall(self):
        self.proces = 2


def dis(x1, y1, x2, y2):
    return math.sqrt(abs(x1 - x2) ** 2 + abs(y1 - y2) ** 2)


def collide(obj1, obj2):
    ret = []
    if obj2.y + obj2.y1 > obj1.y + obj1.y1 > obj2.y and obj1.x + obj1.x1 \
    > obj2.x and obj1.x < obj2.x + obj2.x1:
        ret.append('upcollide')
    if obj2.y + obj2.y1 > obj1.y > obj2.y and obj1.x + obj1.x1 > obj2.x \
    and obj1.x < obj2.x + obj2.x1:
        ret.append('downcollide')
    if 'upcollide' in ret:
        if 'downcollide' in ret:
            if obj1.x < obj2.x:
                return 'leftcollide'
            elif obj1.x + obj1.x1 > obj2.x + obj2.x1:
                return 'rightcollide'
            else:
                return 'incollide'
        else:
            return 'upcollide'
    elif 'downcollide' in ret:
        return 'downcollide'


class bullet:

    def __init__(self, x, y, direction, obj):
        self.x = x
        self.y = y
        self.indef = 'bullet'
        self.direction = direction
        self.image = pygame.image.load('bullet.png')
        self.obj = obj
        self.life = 1
        if self.direction == 0:
            self.x -= 5
        elif self.direction == 1:
            self.x += 20

    def be(self):
        if self.life:
            self.obj.screen.blit(self.image, (self.x - self.obj.camera_x +
            self.obj.screen.get_size()[0] / 2, self.y - self.obj.camera_y +
            self.obj.screen.get_size()[1] / 2))
            if self.direction == 0:
                self.x -= 5
            elif self.direction == 1:
                self.x += 5
            if self.x + 10 < 0:
                self.life = 0
            if self.x > 4500:
                self.life = 0
            if self.x + 10 > self.obj.player.x and self.x < \
            self.obj.player.x + 20:
                if self.y + 10 > self.obj.player.y and self.y < \
                self.obj.player.y + 20:
                    self.obj.player.life -= 1
                    self.life = 0
            for obj in self.obj.objects:
                if obj.type == 'player':
                    if obj.life:
                        if self.x + 10 > obj.x and self.x < obj.x + 20:
                            if self.y + 10 > obj.y and self.y < obj.y + 20:
                                obj.life -= 1
                                self.life = 0
                                pygame.mixer.Sound.play(hurt)


class player:

    def __init__(self, x, y, images, obj):
        self.x = x
        self.y = y
        self.p_x = x
        self.p_y = y
        self.p_dir = 0
        self.images = images
        self.x1 = 8
        self.y1 = 15
        self.obj = obj
        self.lives = 100
        self.fy = 0
        self.jump = 0
        self.jump_sound = pygame.mixer.Sound('jump.aiff')
        self.walk = []
        for x in range(4):
            self.walk.append(pygame.mixer.Sound('player_run' + str(x + 1)
            + '.aiff'))
        self.type = 'player'
        self.direction = 0
        self.anime = 1
        self.anime_go = 1
        self.shot_time = pygame.time.get_ticks()
        self.non_diable = pygame.time.get_ticks()
        self.laser = 20
        self.keys = []
        self.un_lock = ['unlocked', 'locked']
        self.money = 0
        self.obj.camera_x = self.x
        self.obj.camera_y = self.y

    def be(self):
        if self.lives > 0:
            self.fy += 0.12
            self.fy = min(5, self.fy)
            self.fy = max(-5, self.fy)
            self.y += self.fy
            self.obj.screen.blit(self.images[self.direction][self.anime],
            (self.obj.screen.get_size()[0] / 2, self.obj.screen.get_size()
            [1] / 2))
            self.obj.camera_x = self.x
            self.obj.camera_y = self.y
            for life in range(self.lives):
                pygame.draw.rect(self.obj.screen, (255, 0, 0),
                pygame.Rect(self.obj.screen.get_size()[0] - 30,
                self.obj.screen.get_size()[1] - life * 5, 10, 5))
            for las in range(int(self.laser)):
                pygame.draw.rect(self.obj.screen, (255, 255, 0),
                pygame.Rect(self.obj.screen.get_size()[0] - 45,
                self.obj.screen.get_size()[1] - las * 25, 10, 25))
            self.money_text = self.obj.normal.render(str(self.money) + '$',
            1, (0, 0, 0))
            self.obj.screen.blit(self.money_text, (0, 0))
            if self.multyplayer:
                self.ds = ''
                if int(self.x) != int(self.p_x):
                    self.ds += 'x|' + str(int(self.x))
                if int(self.y) != int(self.p_y):
                    self.ds += 'y|' + str(int(self.y))
                if self.direction != self.p_dir:
                    self.ds += 'dir|' + str(self.direction)
                if self.obj.layer != self.obj.p_layer:
                    self.ds += 'lay|' + self.obj.layer
                if self.obj.place != self.obj.p_place:
                    self.ds += 'pl|' + self.obj.place
                if self.ds:
                    self.obj.user_profile.storeData(self.obj.username,
                    self.ds)
                self.p_x = self.x
                self.p_y = self.y
                self.p_dir = self.direction

    def go(self, key):
        self.key = key
        if self.key == 'up':
            if self.jump:
                self.fy -= 5.6
                self.jump = 0
                self.jump_sound.play()
        if self.key == 'right':
            self.x += 3
            self.direction = 1
            self.anime += self.anime_go
            if self.anime in (0, 3):
                self.anime_go *= -1
            for x in range(4):
                self.walk[x].stop()
            self.walk[random.randrange(4)].play()
        if self.key == 'down':
            self.fy += 0.5
        if self.key == 'left':
            self.x -= 3
            self.direction = 0
            self.anime += self.anime_go
            if self.anime in (0, 3):
                self.anime_go *= -1
            for x in range(4):
                self.walk[x].stop()
            self.walk[random.randrange(4)].play()

    def collide(self, collidor, other):
        self.collidor = collidor
        self.other = other
        self.collider = self.other.type
        if self.collidor == 'upcollide':
            if self.collider in ('wall', 'platform'):
                if self.y + self.y1 < self.other.y + 5:
                    self.y = self.other.y - self.y1
                    if self.fy >= 0:
                        self.fy = 0
                        self.jump = 1
                else:
                    if self.x < self.other.x:
                        self.x -= 3
                    else:
                        self.x += 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.y + self.y1 > self.other.y + 3:
                        self.y -= 1
                        if self.fy > 0:
                            self.fy = 0
                            self.jump = 1
                if self.obj.shift:
                        if self.other.key in self.keys:
                            self.other.locked = not self.other.locked
                            self.obj.change('m_You ' + self.un_lock
                            [self.other.locked] + ' the door')
                        else:
                            self.obj.change('m_You don\'t have key ' +
                            self.other.key)
        if self.collidor == 'rightcollide':
            if self.collider == 'wall':
                self.x += 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.x < self.other.x + self.other.x1 - 3:
                        self.x += 3
                if self.obj.shift:
                    if self.other.key in self.keys:
                        self.other.locked = not self.other.locked
                        self.obj.change('m_You ' + self.un_lock
                        [self.other.locked] + ' the door')
                    else:
                        self.obj.change('m_You don\'t have key ' +
                        self.other.key)
        if self.collidor == 'leftcollide':
            if self.collider == 'wall':
                self.x -= 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.x + self.x1 > self.other.x + 3:
                        self.x -= 3
                if self.obj.shift:
                        if self.other.key in self.keys:
                            self.other.locked = not self.other.locked
                            self.obj.change('m_You ' + self.un_lock
                            [self.other.locked] + ' the door')
                        else:
                            self.obj.change('m_You don\'t have key ' +
                            self.other.key)
        if self.collidor == 'downcollide':
            if self.collider == 'wall':
                if self.y > self.other.y + self.other.y1 - 5:
                    self.y = self.other.y + self.other.y1
                    if self.fy <= 0:
                        self.fy = 0
                        self.jump = 1
                    self.jump = False
                else:
                    if self.x < self.other.x:
                        self.x -= 3
                    else:
                        self.x += 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.y < self.other.y + self.other.y1 - 3:
                        self.y -= 1
                        if self.fy < 0:
                            self.fy = 0
                if self.obj.shift:
                        if self.other.key in self.keys:
                            self.other.locked = not self.other.locked
                            self.obj.change('m_You ' + self.un_lock
                            [self.other.locked] + ' the door')
                        else:
                            self.obj.change('m_You don\'t have key ' +
                            self.other.key)
        if self.collider == 'door':
            if self.collidor:
                if self.obj.space:
                    if self.other.locked:
                        self.obj.change('m_This door are locked')
                    else:
                        if self.obj.layer == 'picture':
                            self.obj.layer = 'house'
                        else:
                            self.obj.layer = 'picture'
                    self.obj.space = False
                if self.obj.shift:
                    if self.other.key in self.keys:
                        self.other.locked = not self.other.locked
                        self.obj.change('m_You ' + self.un_lock
                        [self.other.locked] + ' the door')
                    else:
                        self.obj.change('m_You don\'t have key ' +
                        self.other.key)
        if self.collider == 'desk':
            if self.collidor:
                if self.obj.space:
                    self.send = 'm_You get key: '
                    if self.other.used:
                        self.obj.change('m_You found nothing')
                    else:
                        if self.other.key:
                            self.keys.append(self.other.key)
                            self.send += self.other.key + ' and '
                            self.other.key = None
                        self.send += str(self.other.money) + '$.'
                        self.money += self.other.money
                        self.obj.change(self.send)
                        self.obj.case.append('p0')
                        self.other.money = 0
                        self.other.used = True
        if self.collider == 'toilet':
            if self.collidor:
                if self.obj.space:
                    self.obj.change("m_You have successfully used the" +
                    "toilet!")
                    self.y = 3000
                    self.obj.layer = 'picture'
                    self.obj.case.append('p0')
        if self.collider == 'place':
            if self.collidor == 'incollide':
                self.obj.place = self.other.name

    def fire(self):
        self.obj.bullets.append(bullet(self.x, self.y + 6, self.direction))


class man:

    def __init__(self, x, y, images, obj, anime, name, layer, place,
        lives):
        self.x = x
        self.y = y
        self.images = images
        self.x1 = 8
        self.y1 = 15
        self.obj = obj
        self.anime = anime
        self.anime_go = 1
        self.jump = 0
        self.fy = 0
        self.name = name
        self.layer = layer
        self.place = place
        self.type = 'player'
        self.direction = 0
        self.lives = lives

    def be(self):
        if self.layer == self.obj.layer and self.place == self.obj.place:
            if self.lives:
                self.obj.screen.blit(self.images[self.direction]
                [self.anime], (self.x - self.obj.camera_x +
                self.obj.screen.get_size()[0] / 2, self.y -
                self.obj.camera_y + self.obj.screen.get_size()[1] / 2))


class g_man(man):

    def be(self):
        if self.lives:
            if self.layer == self.obj.layer and self.place == \
            self.obj.place:
                self.obj.screen.blit(self.images[self.direction]
                [self.anime], (self.x - self.obj.camera_x +
                self.obj.screen.get_size()[0] / 2, self.y -
                self.obj.camera_y + self.obj.screen.get_size()[1] / 2))
            self.fy += 0.12
            self.fy = min(5, self.fy)
            self.fy = max(-5, self.fy)
            self.y += self.fy
            self.think()

    def think(self):
        pass

    def go(self, key):
        self.key = key
        if self.key == 'up':
            if self.jump:
                self.fy -= 5.6
                self.jump = 0
        if self.key == 'right':
            self.x += 3
            self.direction = 1
            self.anime += self.anime_go
            if self.anime in (0, 3):
                self.anime_go *= -1
        if self.key == 'down':
            self.fy += 0.5
        if self.key == 'left':
            self.x -= 3
            self.direction = 0
            self.anime += self.anime_go
            if self.anime in (0, 3):
                self.anime_go *= -1

    def fire(self):
        pass


class policeman(g_man):

    def think(self):
        if self.x > self.info[0]:
            self.go('left')
        else:
            self.go('right')
        if self.y - 5 > self.info[1]:
            self.go('up')
        if self.y + self.y1 < self.info[1]:
            self.go('down')
        if self.y + 15 > self.info[1] and self.y < self.info[1] + 15:
            self.fire()
        if self.change:
            self.l_info = self.info

    def collide(self, collidor, other):
        self.collidor = collidor
        self.other = other
        self.collider = self.other.type
        if self.collidor == 'upcollide':
            if self.collider in ('wall', 'platform'):
                if self.y + self.y1 < self.other.y + 5:
                    self.y = self.other.y - self.y1
                    if self.fy >= 0:
                        self.fy = 0
                        self.jump = 1
                else:
                    if self.x < self.other.x:
                        self.x -= 3
                    else:
                        self.x += 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.y + self.y1 > self.other.y + 3:
                        self.y -= 1
                        if self.fy > 0:
                            self.fy = 0
                            self.jump = 1
        if self.collidor == 'rightcollide':
            if self.collider == 'wall':
                self.x += 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.x < self.other.x + self.other.x1 - 3:
                        self.x += 3
        if self.collidor == 'leftcollide':
            if self.collider == 'wall':
                self.x -= 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.x + self.x1 > self.other.x + 3:
                        self.x -= 3
        if self.collidor == 'downcollide':
            if self.collider == 'wall':
                if self.y > self.other.y + self.other.y1 - 5:
                    self.y = self.other.y + self.other.y1
                    if self.fy <= 0:
                        self.fy = 0
                        self.jump = 1
                    self.jump = False
                else:
                    if self.x < self.other.x:
                        self.x -= 3
                    else:
                        self.x += 3
            if self.collider == 'passage':
                if self.other.locked:
                    if self.y < self.other.y + self.other.y1 - 3:
                        self.y -= 1
                        if self.fy < 0:
                            self.fy = 0
        if self.collider == 'door':
            if self.collidor:
                if self.l_info[2] != self.info[2] != self.layer:
                    if self.other.locked:
                        pass
                    else:
                        if self.layer == 'picture':
                            self.layer = 'house'
                        else:
                            self.layer = 'picture'
                    self.obj.space = False
        if self.collider in ('player', 'zombie'):
            if dis(self.x, self.y, self.other.x, self.other.y) < 400:
                if self.obj.layer == self.layer:
                    self.info = (other.x, other.y, self.obj.layer)
                    self.change = True
                else:
                    if self.change:
                        self.info = (other.x, other.y, self.obj.layer)
                    self.change = False
            else:
                self.change = False


class wall:

    def __init__(self, x, y, x1, y1):
        self.x = x
        self.y = y
        self.x1 = x1
        self.y1 = y1
        self.type = 'wall'
        self.lives = 1

    def collide(self, collidor, collider):
        pass


class platform:

    def __init__(self, x, y, x1, y1):
        self.x = x
        self.y = y
        self.x1 = x1
        self.y1 = y1
        self.type = 'platform'
        self.lives = 1

    def collide(self, collidor, collider):
        pass


class door:

    def __init__(self, x, y, x1, y1, obj, key, locked=False):
        self.x = x
        self.y = y
        self.x1 = x1
        self.y1 = y1
        self.type = 'door'
        self.lives = 1
        self.obj = obj
        self.key = key
        self.locked = locked

    def collide(self, collidor, collider):
        pass

    def be(self):
        self.obj.screen.blit(self.obj.locked[self.locked], (self.x - 5 -
        self.obj.camera_x + self.obj.screen.get_size()[0] / 2, self.y -
        self.obj.camera_y + self.obj.screen.get_size()[1] / 2))


class passage:

    def __init__(self, x, y, x1, y1, key, obj, locked=True):
        self.x = x
        self.y = y
        self.x1 = x1
        self.y1 = y1
        self.type = 'passage'
        self.lives = 1
        self.locked = locked
        self.key = key
        self.obj = obj
        if self in self.obj.passages1:
            self.layer = 'picture'
        else:
            self.layer = 'house'

    def collide(self, collidor, collider):
        pass

    def be(self):
        self.obj.screen.blit(self.obj.locked[self.locked], (self.x - 3 -
        self.obj.camera_x + self.obj.screen.get_size()[0] / 2, self.y -
        self.obj.camera_y + self.obj.screen.get_size()[1] / 2))


class desk:

    def __init__(self, x, y, x1, y1, regeneration, key=None, money=0,
        used=False):
        self.x = x
        self.y = y
        self.x1 = x1
        self.y1 = y1
        self.regeneration = regeneration
        self.start_key = key
        self.key = key
        self.money = money
        self.start_money = money
        self.used = used
        self.type = 'desk'

    def be(self):
        if self.used:
            self.ran = random.randint(0, self.regeneration)
            if self.ran == self.regeneration:
                self.used = True
                self.key = self.start_key
                self.money = self.start_money


class toilet:

    def __init__(self, x, y, x1, y1):
        self.x = x
        self.y = y
        self.x1 = x1
        self.y1 = y1
        self.type = 'toilet'


class place:

    def __init__(self, x, y, x1, y1, name):
        self.x = x
        self.y = y
        self.x1 = x1
        self.y1 = y1
        self.name = name
        self.type = 'place'

    def collide(self):
        pass


class game:

    def __init__(self):
        self.screen = pygame.display.set_mode((0, 0), FULLSCREEN)
        self.running = True
        self.play = False
        pygame.init()
        pygame.mixer.init(44100, 16, 1, 4096)
        self.mouse = False
        self.normal = pygame.font.SysFont('Arial', 44)
        self.small = pygame.font.SysFont('Arial', 22)
        self.exit_text = self.normal.render('Exit', 1, (0, 0, 0))
        self.exit = pusher(550, 600, self.exit_text, self, 'blackscreen',
        time=5000)
        self.start_text = self.normal.render('Singleplayer', 1, (100, 100,
        100))
        self.start = pusher(525, 100, self.start_text, self, 'shine',
        time=2000)
        self.main_text = self.normal.render('Main Menu', 1, (40, 40, 40))
        self.main_menu = pusher(550, 400, self.main_text, self, 'menu',
        proces=0)
        self.continue_text = self.normal.render('Continue', 1, (60, 60,
        60))
        self.Continue = pusher(550, 200, self.continue_text, self,
        'continue', proces=0)
        self.Log_in_text = self.normal.render('Log in with Game Jolt', 1,
        (60, 60, 60))
        self.Log_in = pusher(475, 300, self.Log_in_text, self,
        do2='log in', time=300, infinity=1)
        self.text_box = pygame.image.load("text_box.bmp")
        self.username = ''
        self.username_text = self.small.render('username:', 1, (0, 0, 255))
        self.user_box = pusher(170, 300, self.text_box, self, 'user',
        infinity=2, proces=0)
        self.token = ''
        self.token_text = self.small.render('token:', 1, (0, 0, 255))
        self.token_box = pusher(170, 400, self.text_box, self, 'token',
        infinity=2, proces=0)
        self.Log_as_guest = self.normal.render('Log in as guest', 1, (40,
        40, 40))
        self.Log_guest = pusher(500, 400, self.Log_as_guest, self,
        do2='Log_in', time=300,
        infinity=1)
        self.multy = self.normal.render('Multyplayer', 1, (80, 80, 80))
        self.multyplayer = pusher(530, 200, self.multy, self, 'shine2')
        self.option = self.normal.render('Options', 1, (20, 20, 20))
        self.options = pusher(550, 500, self.option, self, 'option')
        self.players = {}
        self.policemans = []
        self.case = []
        self.Shift = False
        self.logging = False
        self.Logging = False
        self.walls1 = []
        self.walls2 = []
        self.doors = []
        self.desks = []
        self.passages1 = []
        self.passages2 = []
        self.bullets = []
        self.layer = 'picture'
        self.place = 'ground'
        self.p_layer = 'picture'
        self.p_place = 'ground'
        self.places = []
        self.tilemap = 'tilemap.tmx'
        self.level = tiledtmxloader.tmxreader.TileMapParser().\
        parse_decode(self.tilemap)
        self.resources = tiledtmxloader.helperspygame.\
        ResourceLoaderPygame()
        self.resources.load(self.level)
        self.display = tiledtmxloader.helperspygame.RendererPygame()
        self.objs = tiledtmxloader.helperspygame.\
        get_layers_from_map(self.resources)
        self.player_images = [[], []]
        for dir_ in range(2):
            for anim in range(4):
                self.player_images[dir_].append(pygame.image.load("man_" +
                str(dir_ + 1) + "_" + str(anim + 1) + ".png"))
        self.police_images = [[], []]
        for dir_ in range(2):
            for anim in range(4):
                self.police_images[dir_].append(pygame.image.load
                ("policeman_" + str(dir_ + 1) + "_" + str(anim + 1) +
                ".png"))
        for obj in self.objs:
            if obj.is_object_group:
                if obj.name == 'toilets':
                    for some in obj.objects:
                        self.toilet = toilet(some.x, some.y, some.width,
                        some.height)
                if obj.name == 'layers':
                    for some in obj.objects:
                        self.places.append(place(some.x, some.y,
                        some.width, some.height, some.name))
                if obj.name == 'characters':
                    for some in obj.objects:
                        if some.name == 'player':
                            self.player = player(some.x, some.y,
                            self.player_images, self)
                        if some.name == 'policeman':
                            self.Policeman = policeman(some.x, some.y,
                            self.police_images, self, 0, None, 'picture',
                            'ground', 3)
                            self.Policeman.type = 'policeman'
                            self.Policeman.info = (2250, 2960, 'picture')
                            self.Policeman.l_info = (2250, 2960, 'picture')
                            self.policemans.append(self.Policeman)
                if obj.name == 'desks':
                    for some in obj.objects:
                        self.res = {"x": some.x, "y": some.y, "x1":
                        some.width, "y1": some.height,
                        "regeneration": int(some.properties
                        ["regeneration"])}
                        if 'money' in some.properties:
                            self.res["money"] = int(some.properties
                            ['money'])
                        if 'key' in some.properties:
                            self.res['key'] = some.properties['key']
                        self.desks.append(desk(**self.res))
                if obj.name == 'passages1':
                    for some in obj.objects:
                        self.res = {"x": some.x, "y": some.y, "x1":
                        some.width, "y1": some.height, "key":
                        some.properties['key'], "obj": self}
                        if 'locked' in some.properties:
                            self.res["locked"] = int(bool(some.properties
                            ['locked']))
                        self.passages1.append(passage(**self.res))
                if obj.name == 'passages2':
                    for some in obj.objects:
                        self.res = {"x": some.x, "y": some.y, "x1":
                        some.width, "y1": some.height, "key":
                        some.properties['key'], "obj": self}
                        if 'locked' in some.properties:
                            self.key["locked"] = int(bool(some.properties
                            ['locked']))
                        self.passages2.append(passage(**self.res))
                if obj.name == 'walls1':
                    for some in obj.objects:
                        self.walls1.append(wall(some.x, some.y, some.width,
                        some.height))
                if obj.name == 'platforms1':
                    for some in obj.objects:
                        self.walls1.append(platform(some.x, some.y,
                        some.width, some.height))
                if obj.name == 'walls2':
                    for some in obj.objects:
                        self.walls2.append(wall(some.x, some.y, some.width,
                        some.height))
                if obj.name == 'platforms2':
                    for some in obj.objects:
                        self.walls2.append(platform(some.x, some.y,
                        some.width, some.height))
                if obj.name == 'doors':
                    for some in obj.objects:
                        self.res = {"x": some.x, "y": some.y, "x1":
                        some.width, "y1": some.height, "obj": self, "key":
                        some.properties["key"]}
                        if "locked" in some.properties:
                            self.res["locked"] = some.properties["locked"]
                        self.doors.append(door(**self.res))
        self.locked = []
        self.locked.append(pygame.image.load("unlocked.png"))
        self.locked.append(pygame.image.load("locked.png"))
        self.message = None
        self.display.set_camera_position_and_size(self.camera_x,
        self.camera_y, self.screen.get_size()[0], self.screen.get_size()
        [1], "center")

    def main(self):
        while self.running:
            self.space = False
            self.shift = False
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    self.running = False
                if event.type == pygame.MOUSEBUTTONDOWN:
                    self.mouse = True
                else:
                    self.mouse = False
                if event.type == KEYDOWN:
                    if event.key == pygame.K_ESCAPE:
                        if self.play:
                            self.play = False
                            self.exit.recall()
                            self.Continue.recall()
                            self.main_menu.recall()
                    if event.key == pygame.K_SPACE:
                        self.space = True
                    if event.key == pygame.K_RSHIFT:
                        self.shift = True
                    if event.key == pygame.K_RCTRL or event.key == \
                    pygame.K_LCTRL:
                        self.Ctrl = True
                    if self.logging or self.Logging:
                        if 31 < event.key < 126:
                            if self.choose == 'user':
                                if self.Shift:
                                    self.username += chr(event.key).upper()
                                else:
                                    self.username += chr(event.key)
                            if self.choose == 'token':
                                if self.Shift:
                                    self.token += chr(event.key).upper()
                                else:
                                    self.token += chr(event.key)
                        if event.key == pygame.K_BACKSPACE:
                            if self.choose == 'user':
                                self.username = self.username[:-1]
                            if self.choose == 'token':
                                self.token = self.token[:-1]
            self.screen.fill((255, 255, 255))
            if self.play:
                for obj in self.objs:
                    if obj.is_object_group:
                        pass
                    else:
                        if self.layer in obj.name and self.place in \
                        obj.name:
                            self.display.render_layer(self.screen, obj)
                        if obj.name == 'door_' + self.place:
                            self.display.render_layer(self.screen, obj)
                if self.layer == 'picture':
                    for wall in self.walls1:
                        self.player.collide(collide(self.player, wall),
                        wall)
                        for police in self.policemans:
                            police.collide(collide(police, wall), wall)
                    for passage in self.passages1:
                        self.player.collide(collide(self.player, passage),
                        passage)
                        passage.be()
                        for police in self.policemans:
                            police.collide(collide(police, passage),
                            passage)
                else:
                    for wall in self.walls2:
                        self.player.collide(collide(self.player, wall),
                        wall)
                        for police in self.policemans:
                            police.collide(collide(police, wall), wall)
                    for passage in self.passages2:
                        self.player.collide(collide(self.player, passage),
                        passage)
                        passage.be()
                        for police in self.policemans:
                            police.collide(collide(police, passage),
                            passage)
                for Place in self.places:
                    self.player.collide(collide(self.player, Place), Place)
                    for police in self.policemans:
                        police.collide(collide(police, Place), Place)
                for door in self.doors:
                    self.player.collide(collide(self.player, door), door)
                    door.be()
                    for police in self.policemans:
                        police.collide(collide(police, door), door)
                for Desk in self.desks:
                    self.player.collide(collide(self.player, Desk), Desk)
                    Desk.be()
                self.player.collide(collide(self.player, self.toilet),
                self.toilet)
                self.player.be()
                self.display.set_camera_position_and_size(self.camera_x,
                self.camera_y, self.screen.get_size()[0],
                self.screen.get_size()[1], "center")
                self.press = pygame.key.get_pressed()
                if self.press[pygame.K_LEFT]:
                    self.player.go('left')
                if self.press[pygame.K_UP]:
                    self.player.go('up')
                if self.press[pygame.K_RIGHT]:
                    self.player.go('right')
                if self.press[pygame.K_DOWN]:
                    self.player.go('down')
                if self.message:
                    self.screen.blit(self.message, (self.m_x, 10))
                    if pygame.time.get_ticks() - 4000 > self.m_time:
                        self.message = None
                for police in self.policemans:
                    police.collide(collide(police, self.player),
                    self.player)
                    police.be()
                self.counter = 0
                for inv in self.case:
                    if inv[:1] == 'p':
                        self.end = int(inv[1:])
                        self.end += 1
                        self.case[self.counter] = 'p' + str(self.end)
                        if self.end > 1400:
                            del self.case[self.counter]
                        self.put = random.randint(0, 700)
                        if self.put == 100:
                            self.Policeman = policeman(random.randint(0,
                            4480), 2960, self.police_images, self, 0, None,
                            'picture', 'ground', 3)
                            self.Policeman.type = 'policeman'
                            self.Policeman.info = (2250, 2960, 'picture')
                            self.Policeman.l_info = (2250, 2960, 'picture')
                            self.policemans.append(self.Policeman)
                            del self.case[self.counter]
                    self.counter += 1
                for bull in self.bullets:
                    bull.be()
            self.press = pygame.key.get_pressed()
            if self.press[pygame.K_RSHIFT] or self.press[pygame.K_LSHIFT]:
                self.Shift = True
            else:
                self.Shift = False
            self.exit.be()
            self.start.be()
            self.main_menu.be()
            self.Log_in.be()
            self.token_box.be()
            self.user_box.be()
            self.Continue.be()
            self.Log_guest.be()
            self.options.be()
            self.multyplayer.be()
            if self.logging:
                self.username_ans = self.small.render(self.username, 1, (0,
                0, 255))
                self.token_ans = self.small.render(self.token, 1, (0, 0,
                255))
                self.screen.blit(self.username_ans, (172, 302))
                self.screen.blit(self.token_ans, (172, 402))
                self.screen.blit(self.username_text, (90, 300))
                self.screen.blit(self.token_text, (120, 400))
            if self.Logging:
                self.username_ans = self.small.render(self.username, 1, (0,
                0, 255))
                self.screen.blit(self.username_ans, (172, 302))
                self.screen.blit(self.username_text, (90, 300))
            self.p_layer = self.layer
            self.p_place = self.place
            pygame.display.flip()

    def change(self, what):
        self.what = what
        if self.what == 'blackscreen':
            self.all = 255
            while self.running:
                self.screen.fill((self.all, self.all, self.all))
                self.exit.be()
                self.start.be()
                self.Log_in.be()
                self.Log_guest.be()
                self.options.be()
                self.multyplayer.be()
                self.all -= 1
                if self.all == 0:
                    self.running = False
                pygame.display.flip()
        if self.what:
            if 'shine' in self.what:
                self.screen.fill((random.randint(0, 255), random.randint(0,
                255), random.randint(0, 255)))
                pygame.display.flip()
                pygame.time.wait(500)
                self.exit.proces = 0
                self.Log_in.proces = 0
                self.Log_guest.proces = 0
                self.multyplayer.proces = 0
                self.options.proces = 0
                self.user_profile = py_gjapi.GameJoltTrophy(self.username,
                self.token, 34824, '****************') #in real code here goes private key
                if self.user_profile.authenticateUser():
                    self.guest = False
                    self.user_profile.storeData(self.username, 'started')
                    if '2' in self.what:
                        self.players = self.user_profile.getDataKeys()
                        ['keys']
                        self.player.multyplayer = True
                    else:
                        self.player.multyplayer = False
                else:
                    self.guest = True
                    if '2' in self.what:
                        self.players = self.user_profile.getDataKeys()
                        ['keys']
                        self.player.multyplayer = True
                    else:
                        self.player.multyplayer = False
                self.play = True
        if self.what == 'menu':
            self.start.recall()
            self.Continue.proces = 0
        if self.what == 'continue':
            self.main_menu.proces = 0
            self.exit.proces = 0
            self.play = True
        if self.what == 'log in':
            if self.logging:
                self.logging = False
                self.start.recall()
                self.exit.recall()
                self.multyplayer.recall()
                self.Log_guest.recall()
                self.options.recall()
                self.user_box.proces = 0
                self.token_box.proces = 0
            else:
                self.logging = True
                self.exit.proces = 0
                self.start.proces = 0
                self.multyplayer.proces = 0
                self.Log_guest.proces = 0
                self.options.proces = 0
                self.token = ''
                self.username = ''
                self.choose = None
                self.user_box.recall()
                self.token_box.recall()
        if self.what == 'Log_in':
            if self.Logging:
                self.Logging = False
                self.start.recall()
                self.exit.recall()
                self.multyplayer.recall()
                self.Log_in.recall()
                self.options.recall()
                self.user_box.proces = 0
            else:
                self.Logging = True
                self.exit.proces = 0
                self.start.proces = 0
                self.multyplayer.proces = 0
                self.Log_in.proces = 0
                self.options.proces = 0
                self.username = ''
                self.choose = None
                self.user_box.recall()
        if self.what:
            if len(self.what) > 2:
                if self.what[:2] == 'm_':
                    self.message = self.normal.render(self.what[2:], 1, (0,
                    0, 0))
                    self.m_time = pygame.time.get_ticks()
                    self.m_x = self.screen.get_size()[0] / 2 - \
                    self.message.get_size()[0] / 2
        if self.what in ('user', 'token'):
            self.choose = self.what

Game = game()
Game.main()
