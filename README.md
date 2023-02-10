# gamemom
uvu
import tkinter
import random


def move_wrap():
    x = canvas.coords(player)[0]
    y = canvas.coords(player)[1]
    if x < 0:
        canvas.move(player, 600, 0)
    if x > 590:
        canvas.move(player, -600, 0)
    if y < 0:
        canvas.move(player, 0, 600)
    elif y > 590:
        canvas.move(player, 0, -600)


def fires_move(): # Передвижение противников
    for f in fires:
        if canvas.coords(player)[0] > canvas.coords(f)[0]:
            canvas.move(f, step, 0)
        elif canvas.coords(player)[0] < canvas.coords(f)[0]:
            canvas.move(f, -step, 0)
        elif canvas.coords(player)[1] < canvas.coords(f)[1]:
            canvas.move(f,0, -step)
        elif canvas.coords(player)[1] > canvas.coords(f)[1]:
            canvas.move(f,0, step)


def support():
    help_txt.config(text='Кнопка R позволяет уничтожить случайного врага на карте')

def do_nothing(x):
    pass


def check_move():
    if canvas.coords(player) == canvas.coords(exit):
        label.config(text="Победа!")
        master.bind("<KeyPress>", do_nothing)
    for f in fires:
        if canvas.coords(player) == canvas.coords(f):
            label.config(text="Ты проиграл!")
            master.bind("<KeyPress>", do_nothing)


def key_pressed(event):
    if event.keysym == 'Up':
        canvas.move(player, 0, -step)
    if event.keysym == 'Down':
        canvas.move(player, 0, step)
    if event.keysym == 'Right':
        canvas.move(player, step, 0)
    if event.keysym == 'Left':
        canvas.move(player, -step, 0)
    if event.keysym == 'r': # Способность которая вместо хода позволяет уничтожить противника
        canvas.delete(fires[0])
        del fires[0]
    if max(canvas.coords(player)) > 600 or min(canvas.coords(player)) < 0:
        move_wrap()
    fires_move()
    check_move()


def prepare_and_start():
    global player, exit, fires
    canvas.delete("all")
    player_pos = (random.randint(1, N_X - 1) * step,
                  random.randint(1, N_Y - 1) * step)
    exit_pos = (random.randint(1, N_X - 1) * step,
                random.randint(1, N_Y - 1) * step)
    player = canvas.create_oval(
        (player_pos[0], player_pos[1]),
        (player_pos[0] + step, player_pos[1] + step),
        fill='green')
    exit = canvas.create_oval(
        (exit_pos[0], exit_pos[1]),
        (exit_pos[0] + step, exit_pos[1] + step),
        fill='yellow')
    N_FIRES = 7  # Число клеток, заполненных огнем
    fires = []
    fircords = []
    while len(fires) != N_FIRES:
        fire_pos = (random.randint(1, N_X - 1) * step,
                    random.randint(1, N_Y - 1) * step)
        if not fire_pos in fircords:
            fircords.append(fire_pos)
            fire = canvas.create_rectangle(
                (fire_pos[0], fire_pos[1]),
                (fire_pos[0] + step, fire_pos[1] + step),
                fill='red')
            fires.append(fire)
    for f in fires:
        if canvas.coords(exit) == canvas.coords(f) or canvas.coords(player) == canvas.coords(f) or canvas.coords(player) == canvas.coords(exit):
            prepare_and_start()
    label.config(text="Найди выход!")
    master.bind("<KeyPress>", key_pressed)


master = tkinter.Tk()
step = 60
N_X = 10
N_Y = 10
canvas = tkinter.Canvas(master, bg='blue',
                        width=step * N_X, height=step * N_Y)
restart = tkinter.Button(master, text="Начать заново",
                         command=prepare_and_start)
player_pos = (random.randint(0, N_X - 1) * step,
              random.randint(0, N_Y - 1) * step)
exit_pos = (random.randint(0, N_X - 1) * step,
            random.randint(0, N_Y - 1) * step)
player = canvas.create_rectangle((player_pos[0], player_pos[1]),
                            (player_pos[0] + step, player_pos[1] + step),
                            fill='green')
exit = canvas.create_oval((exit_pos[0], exit_pos[1]),
                          (exit_pos[0] + step, exit_pos[1] + step),
                          fill='yellow')

help = tkinter.Button(master, text='Помощь',
                         command=support)
help_txt = tkinter.Label(master, text='')
label = tkinter.Label(master, text="Найди выход")
label.pack()
canvas.pack()
restart.pack()
help.pack()
help_txt.pack()
master.bind("<KeyPress>", prepare_and_start())
master.mainloop()
