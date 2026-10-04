# Рулетка без денег / Free Demo Roulette

**RU** · [English below](#english)

Бесплатная демо-рулетка в одном HTML-файле. Виртуальные фишки, никаких реальных денег, никаких серверов и регистрации. Работает на телефоне и компьютере.

**Демо:** `https://ВАШ_НИК.github.io/roulette/`

## Возможности

- Европейская рулетка: 37 ячеек, один ноль.
- Ставки: числа (35:1), дюжины (2:1), красное/чёрное, чёт/нечет, 1–18 и 19–36 (1:1).
- Плавная анимация колеса и шарика на canvas, подсветка выигрыша, конфетти.
- Звук (синтезируется в браузере через Web Audio, без файлов), кнопка выключения.
- Светлая и тёмная темы, запуск спина смахиванием колеса, кнопка «Повторить».
- Уважает системную настройку «уменьшить движение».

## Как устроена честность

Результат нельзя подогнать задним числом. Схема такая:

1. Перед спином страница создаёт случайный секрет (`crypto.getRandomValues`, 32 байта) и показывает его отпечаток **SHA-256**.
2. Вы можете изменить своё слово (client seed) до спина.
3. Результат вычисляется как число из `SHA-256(секрет:слово:номер_спина)`. Берутся 4-байтовые куски хэша; если значение попадает в «хвост», который сделал бы распределение неравномерным, оно отбрасывается (rejection sampling), иначе результат равен `значение % 37`. Смещения в сторону каких-то чисел нет.
4. После спина открывается секрет. Его отпечаток должен совпасть с тем, что был показан до спина.

Проверить самостоятельно, например на Python:

```python
import hashlib
seed, client, nonce = "СЕКРЕТ", "ВАШЕ_СЛОВО", 0
h = hashlib.sha256(f"{seed}:{client}:{nonce}".encode()).hexdigest()
lim = (2**32 // 37) * 37
for i in range(8):
    v = int(h[i*8:i*8+8], 16)
    if v < lim:
        print(v % 37)
        break
```

**Важная оговорка.** Это демо работает целиком в браузере: и «казино», и игрок находятся на одном устройстве. Схема показывает, как работает проверяемая честность, но для настоящей игры на деньги секрет должен генерироваться и храниться на независимом сервере.

## Запуск

Откройте `index.html` в браузере или выложите репозиторий на GitHub Pages (Settings → Pages → Deploy from a branch → `main` / root).

---

## English

A free demo roulette in a single HTML file. Virtual chips only, no real money, no backend, no sign-up. Works on mobile and desktop.

**Live demo:** `https://YOUR_NAME.github.io/roulette/`

### Features

- European roulette: 37 pockets, a single zero.
- Bets: straight numbers (35:1), dozens (2:1), red/black, odd/even, 1–18 and 19–36 (1:1).
- Smooth canvas wheel and ball animation, win highlights, confetti.
- Sound synthesized in the browser with Web Audio (no audio files), with a mute button.
- Light and dark themes, swipe the wheel to spin, a "Repeat" button for the last bet.
- Respects the system "reduce motion" setting.

### How fairness works

The outcome cannot be changed after the fact:

1. Before each spin the page creates a random secret (`crypto.getRandomValues`, 32 bytes) and shows its **SHA-256** hash.
2. You can edit your own client seed before the spin.
3. The result is derived from `SHA-256(secret:clientSeed:nonce)`. The hash is read in 4-byte chunks; a chunk that would introduce modulo bias is rejected (rejection sampling), otherwise the result is `value % 37`. No pocket is favoured.
4. After the spin the secret is revealed, and its hash must match the one shown beforehand.

You can verify any spin yourself with the Python snippet above.

**Caveat.** Everything runs in your browser, so the "house" and the player are the same device. This demonstrates a provably fair scheme; for real-money use, the secret would have to be generated and held by an independent server.

### Run

Open `index.html` in a browser, or host the repository with GitHub Pages (Settings → Pages → Deploy from a branch → `main` / root).
