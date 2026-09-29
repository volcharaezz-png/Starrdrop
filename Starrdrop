import math
import random
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.gridlayout import GridLayout
from kivy.uix.label import Label
from kivy.uix.button import Button
from kivy.uix.popup import Popup
from kivy.uix.scrollview import ScrollView
from kivy.uix.widget import Widget
from kivy.uix.behaviors import ButtonBehavior
from kivy.graphics import Color, Ellipse, Line, Mesh, Rectangle
from kivy.animation import Animation
from kivy.core.window import Window
from kivy.clock import Clock

Window.clearcolor = (0.05, 0.05, 0.08, 1)

RARITIES = [
    {"name": "RARE", "color": (0.2, 0.85, 0.25, 1)},
    {"name": "SUPER RARE", "color": (0.15, 0.55, 0.95, 1)},
    {"name": "EPIC", "color": (0.65, 0.2, 0.85, 1)},
    {"name": "MYTHIC", "color": (0.95, 0.2, 0.3, 1)},
    {"name": "LEGENDARY", "color": (1, 0.75, 0, 1)}
]

RARITY_WEIGHTS = [50, 28, 15, 5, 2]

REWARD_CONFIG = {
    "RARE": {"coin": (100, 250), "gem": (1, 3), "credit": (20, 50), "weights": [50, 20, 30], "brawler_chance": 0},
    "SUPER RARE": {"coin": (250, 500), "gem": (3, 6), "credit": (50, 100), "weights": [50, 20, 30], "brawler_chance": 0},
    "EPIC": {"coin": (500, 1000), "gem": (6, 12), "credit": (100, 200), "weights": [45, 22, 30], "brawler_chance": 3},
    "MYTHIC": {"coin": (1000, 2500), "gem": (12, 25), "credit": (200, 400), "weights": [40, 25, 27], "brawler_chance": 8},
    "LEGENDARY": {"coin": (2500, 5000), "gem": (30, 75), "credit": (400, 800), "weights": [35, 30, 15], "brawler_chance": 20}
}

BRAWLER_CATALOG = {
    "RARE": {"names": ["El Primo", "Poco", "Barley", "Rosa"], "coin_price": 3000, "gem_price": 99},
    "SUPER RARE": {"names": ["Rico", "Darryl", "Penny", "Carl", "Jacky"], "coin_price": 6000, "gem_price": 179},
    "EPIC": {"names": ["Piper", "Pam", "Frank", "Bibi", "Bea", "Edgar"], "coin_price": 12000, "gem_price": 349},
    "MYTHIC": {"names": ["Mortis", "Tara", "Gene", "Max", "Mr. P", "Sprout"], "coin_price": 25000, "gem_price": 599},
    "LEGENDARY": {"names": ["Spike", "Crow", "Leon", "Sandy", "Amber", "Meg"], "coin_price": 50000, "gem_price": 999}
}

class AnimatedBackgroundWidget(Widget):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.particles = []
        for _ in range(25):
            self.particles.append({
                'x': random.random(),
                'y': random.random(),
                'size': random.uniform(3, 8),
                'speed': random.uniform(0.001, 0.003),
                'alpha': random.uniform(0.2, 0.7)
            })
        self.bind(pos=self.update_canvas, size=self.update_canvas)
        Clock.schedule_interval(self.animate_background, 1 / 30.0)

    def animate_background(self, dt):
        for p in self.particles:
            p['y'] += p['speed']
            if p['y'] > 1.0:
                p['y'] = 0.0
                p['x'] = random.random()
        self.update_canvas()

    def update_canvas(self, *args):
        self.canvas.clear()
        with self.canvas:
            Color(0.06, 0.06, 0.12, 1)
            Rectangle(pos=self.pos, size=self.size)
            for p in self.particles:
                px = self.x + p['x'] * self.width
                py = self.y + p['y'] * self.height
                Color(0.4, 0.6, 1.0, p['alpha'])
                Ellipse(pos=(px, py), size=(p['size'], p['size']))

class StarrDropWidget(ButtonBehavior, Widget):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.drop_color = RARITIES[0]["color"]
        self.bind(pos=self.redraw, size=self.redraw)

    def set_drop_color(self, new_color):
        self.drop_color = new_color
        self.redraw()

    def redraw(self, *args):
        self.canvas.clear()
        cx, cy = self.center_x, self.center_y
        outer_r = min(self.width, self.height) * 0.45
        inner_r = outer_r * 0.52

        vertices, indices, outline_pts = [], [], []
        vertices.extend([cx, cy, 0, 0])

        for i in range(12):
            angle = math.radians(i * 30 - 90)
            r = outer_r if i % 2 == 0 else inner_r
            px = cx + r * math.cos(angle)
            py = cy + r * math.sin(angle)
            vertices.extend([px, py, 0, 0])
            outline_pts.extend([px, py])

        for i in range(1, 13):
            next_idx = 1 if i == 12 else i + 1
            indices.extend([0, i, next_idx])

        outline_pts.extend([outline_pts[0], outline_pts[1]])

        with self.canvas:
            Color(*self.drop_color)
            Mesh(vertices=vertices, indices=indices, mode='triangles')
            Color(0.05, 0.05, 0.05, 1)
            Line(points=outline_pts, width=3.5, close=True)
            face_r = outer_r * 0.62
            Color(*self.drop_color)
            Ellipse(pos=(cx - face_r, cy - face_r), size=(face_r * 2, face_r * 2))
            Color(0.05, 0.05, 0.05, 1)
            Line(ellipse=(cx - face_r, cy - face_r, face_r * 2, face_r * 2), width=3.5)
            eye_r = face_r * 0.28
            eye_off_x = face_r * 0.38
            eye_off_y = face_r * 0.15
            Ellipse(pos=(cx - eye_off_x - eye_r, cy + eye_off_y - eye_r), size=(eye_r * 2, eye_r * 2))
            Ellipse(pos=(cx + eye_off_x - eye_r, cy + eye_off_y - eye_r), size=(eye_r * 2, eye_r * 2))
            mouth_w = face_r * 0.5
            mouth_h = face_r * 0.35
            mouth_y = cy - face_r * 0.38
            Ellipse(pos=(cx - mouth_w / 2, mouth_y - mouth_h / 2), size=(mouth_w, mouth_h))
            Color(*self.drop_color)
            Ellipse(pos=(cx - mouth_w / 2, mouth_y - mouth_h / 2 + mouth_h * 0.35), size=(mouth_w, mouth_h))

class StarrDropGame(BoxLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.orientation = 'vertical'
        self.padding, self.spacing = 15, 10
        self.game_started = False
        self.current_rarity_index = 0
        self.target_rarity_index = 0
        self.attempts_left = 4
        self.ready_to_open = False
        self.is_opened = False
        self.total_coins, self.total_gems, self.total_credits = 0, 0, 0
        self.unlocked_brawlers = []

        self.stats_panel = GridLayout(cols=4, size_hint=(1, 0.12))
        self.coin_label = Label(text="🟡 Coins:\n0", font_size='13sp', bold=True, halign='center')
        self.gem_label = Label(text="💎 Gems:\n0", font_size='13sp', bold=True, halign='center')
        self.credit_label = Label(text="🔵 Credits:\n0", font_size='13sp', bold=True, halign='center')
        self.brawler_label = Label(text="🥊 Brawlers:\n0/27", font_size='13sp', bold=True, halign='center')
        self.stats_panel.add_widget(self.coin_label)
        self.stats_panel.add_widget(self.gem_label)
        self.stats_panel.add_widget(self.credit_label)
        self.stats_panel.add_widget(self.brawler_label)
        self.add_widget(self.stats_panel)

        self.title_label = Label(text="BRAWL STARR DROP", font_size='22sp', bold=True, color=(1, 1, 1, 1), size_hint=(1, 0.08))
        self.add_widget(self.title_label)

        self.starr_button = StarrDropWidget(size_hint=(0.85, 0.50), pos_hint={'center_x': 0.5})
        self.starr_button.bind(on_press=self.on_starr_drop_tap)
        self.add_widget(self.starr_button)

        self.info_label = Label(text="Tap Starr Drop to Start!", font_size='14sp', halign='center', size_hint=(1, 0.10))
        self.add_widget(self.info_label)

        self.nav_panel = BoxLayout(orientation='horizontal', spacing=10, size_hint=(1, 0.12))
        self.shop_button = Button(text="🛒 SHOP", font_size='16sp', bold=True, background_normal='', background_color=(0.9, 0.5, 0.1, 1))
        self.shop_button.bind(on_press=self.open_shop)
        self.collection_button = Button(text="🥊 BRAWLERS", font_size='16sp', bold=True, background_normal='', background_color=(0.1, 0.8, 0.2, 1))
        self.collection_button.bind(on_press=self.open_collection)
        self.nav_panel.add_widget(self.shop_button)
        self.nav_panel.add_widget(self.collection_button)
        self.add_widget(self.nav_panel)

    def trigger_animation(self):
        anim = Animation(size_hint=(0.9, 0.53), duration=0.05) + Animation(size_hint=(0.85, 0.50), duration=0.05)
        anim.start(self.starr_button)

    def update_stats_ui(self):
        self.coin_label.text = f"🟡 Coins:\n{self.total_coins}"
        self.gem_label.text = f"💎 Gems:\n{self.total_gems}"
        self.credit_label.text = f"🔵 Credits:\n{self.total_credits}"
        self.brawler_label.text = f"🥊 Brawlers:\n{len(self.unlocked_brawlers)}/27"

    def start_new_game(self):
        self.game_started = True
        self.is_opened = False
        self.ready_to_open = False
        self.current_rarity_index = 0
        self.attempts_left = 4

        chosen_rarity = random.choices(RARITIES, weights=RARITY_WEIGHTS, k=1)[0]
        self.target_rarity_index = RARITIES.index(chosen_rarity)

        rare = RARITIES[0]
        self.starr_button.set_drop_color(rare["color"])
        self.title_label.text = f"⭐ {rare['name']} STARR DROP ⭐"
        self.title_label.color = rare["color"]
        self.info_label.text = f"Attempts left: {self.attempts_left}"

    def on_starr_drop_tap(self, instance):
        self.trigger_animation()
        if not self.game_started:
            self.start_new_game()
            return
        if self.is_opened:
            self.reset_to_start_screen()
            return
        if self.ready_to_open:
            self.claim_reward()
            return

        self.attempts_left -= 1
        if self.current_rarity_index < self.target_rarity_index:
            self.current_rarity_index += 1
            self.attempts_left = 4
            new_rarity = RARITIES[self.current_rarity_index]
            self.starr_button.set_drop_color(new_rarity["color"])
            self.title_label.text = f"⭐ {new_rarity['name']} STARR DROP ⭐"
            self.title_label.color = new_rarity["color"]
            self.info_label.text = f"UPGRADE! Attempts left: {self.attempts_left}"
        else:
            if self.attempts_left > 0:
                self.info_label.text = f"No upgrade! Attempts left: {self.attempts_left}"
            else:
                self.ready_to_open = True
                self.info_label.text = "No more attempts! Tap to open!"

    def claim_reward(self):
        self.is_opened = True
        rarity_name = RARITIES[self.current_rarity_index]["name"]
        cfg = REWARD_CONFIG[rarity_name]

        brawler_won = False
        if cfg["brawler_chance"] > 0 and random.randint(1, 100) <= cfg["brawler_chance"]:
            brawler_won = True

        reward_text = ""
        if brawler_won:
            available = BRAWLER_CATALOG.get(rarity_name, BRAWLER_CATALOG["EPIC"])["names"]
            locked = [b for b in available if b not in self.unlocked_brawlers]
            if locked:
                brawler = random.choice(locked)
                self.unlocked_brawlers.append(brawler)
                reward_text = f"🥊 NEW BRAWLER!\n{brawler} ({rarity_name})"
            else:
                bonus = cfg["coin"][1] * 2
                self.total_coins += bonus
                reward_text = f"🟡 {bonus} Bonus Coins!"
        else:
            reward_type = random.choices(["coin", "gem", "credit"], weights=cfg["weights"])[0]
            if reward_type == "coin":
                amount = random.randint(*cfg["coin"])
                self.total_coins += amount
                reward_text = f"🟡 {amount} Coins!"
            elif reward_type == "gem":
                amount = random.randint(*cfg["gem"])
                self.total_gems += amount
                reward_text = f"💎 {amount} Gems!"
            elif reward_type == "credit":
                amount = random.randint(*cfg["credit"])
                self.total_credits += amount
                reward_text = f"🔵 {amount} Credits!"

        self.update_stats_ui()
        self.info_label.text = f"REWARD: {reward_text}\nTap to play again!"

    def reset_to_start_screen(self):
        self.game_started = False
        self.is_opened = False
        self.ready_to_open = False
        self.starr_button.set_drop_color(RARITIES[0]["color"])
        self.title_label.text = "BRAWL STARR DROP"
        self.title_label.color = (1, 1, 1, 1)
        self.info_label.text = "Tap Starr Drop to begin!"

    def open_collection(self, instance):
        content = BoxLayout(orientation='vertical', padding=10, spacing=10)
        scroll = ScrollView(size_hint=(1, 1))
        grid = GridLayout(cols=1, spacing=10, size_hint_y=None)
        grid.bind(minimum_height=grid.setter('height'))

        for rarity, data in BRAWLER_CATALOG.items():
            rarity_color = next(r["color"] for r in RARITIES if r["name"] == rarity)
            grid.add_widget(Label(text=f"--- {rarity} ---", font_size='13sp', bold=True, size_hint_y=None, height=30, color=rarity_color))
            for brawler in data["names"]:
                is_unlocked = brawler in self.unlocked_brawlers
                status_text = f"✅ {brawler}" if is_unlocked else f"🔒 {brawler}"
                bg_color = (0.2, 0.8, 0.2, 1) if is_unlocked else (0.4, 0.4, 0.4, 1)
                grid.add_widget(Label(text=status_text, font_size='14sp', size_hint_y=None, height=25, color=bg_color))

        scroll.add_widget(grid)
        content.add_widget(scroll)
        close_btn = Button(text="CLOSE", size_hint=(1, 0.15), bold=True)
        popup = Popup(title="🥊 Brawlers Collection", content=content, size_hint=(0.85, 0.85))
        close_btn.bind(on_press=popup.dismiss)
        content.add_widget(close_btn)
        popup.open()

    def open_shop(self, instance):
        content = BoxLayout(orientation='vertical', padding=10, spacing=10)
        scroll = ScrollView(size_hint=(1, 1))
        grid = GridLayout(cols=1, spacing=12, size_hint_y=None)
        grid.bind(minimum_height=grid.setter('height'))

        for rarity, data in BRAWLER_CATALOG.items():
            locked_brawlers = [b for b in data["names"] if b not in self.unlocked_brawlers]
            if not locked_brawlers: continue
            rarity_color = next(r["color"] for r in RARITIES if r["name"] == rarity)
            grid.add_widget(Label(text=f"🛒 {rarity} BRAWLERS", font_size='15sp', bold=True, size_hint_y=None, height=30, color=rarity_color))

            for brawler in locked_brawlers:
                box = BoxLayout(orientation='vertical', size_hint_y=None, height=90, padding=5)
                box.add_widget(Label(text=f"🥊 {brawler}", font_size='14sp', bold=True, size_hint_y=0.4))
                btn_layout = BoxLayout(orientation='horizontal', spacing=8, size_hint_y=0.6)
                btn_coin = Button(text=f"🟡 {data['coin_price']} Coins", font_size='12sp', bold=True, background_normal='', background_color=(0.9, 0.7, 0.1, 1))
                btn_coin.bind(on_press=lambda btn, b=brawler, p=data['coin_price']: self.buy_brawler(b, "coin", p, popup))
                btn_gem = Button(text=f"💎 {data['gem_price']} Gems", font_size='12sp', bold=True, background_normal='', background_color=(0.1, 0.8, 0.3, 1))
                btn_gem.bind(on_press=lambda btn, b=brawler, p=data['gem_price']: self.buy_brawler(b, "gem", p, popup))
                btn_layout.add_widget(btn_coin)
                btn_layout.add_widget(btn_gem)
                box.add_widget(btn_layout)
                grid.add_widget(box)

        scroll.add_widget(grid)
        content.add_widget(scroll)
        close_btn = Button(text="CLOSE SHOP", size_hint=(1, 0.15), bold=True)
        popup = Popup(title="🛒 Brawler Shop", content=content, size_hint=(0.9, 0.85))
        close_btn.bind(on_press=popup.dismiss)
        content.add_widget(close_btn)
        popup.open()

    def buy_brawler(self, brawler_name, currency_type, price, popup):
        if currency_type == "coin" and self.total_coins >= price:
            self.total_coins -= price
            self.unlocked_brawlers.append(brawler_name)
        elif currency_type == "gem" and self.total_gems >= price:
            self.total_gems -= price
            self.unlocked_brawlers.append(brawler_name)
        else:
            self.info_label.text = "Not enough currency!"
            return
        self.update_stats_ui()
        popup.dismiss()

class StarrDropApp(App):
    def build(self):
        root = Widget()
        bg = AnimatedBackgroundWidget(size=Window.size)
        game = StarrDropGame(size=Window.size)
        def update_rects(instance, value):
            bg.size, game.size = value, value
        Window.bind(size=update_rects)
        root.add_widget(bg)
        root.add_widget(game)
        return root

if __name__ == '__main__':
    StarrDropApp().run()
