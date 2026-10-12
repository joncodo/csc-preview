(() => {
    const wall = document.querySelector('.cake-wall');
    const data = document.querySelector('#csc-cake-pool');
    const control = document.querySelector('.wall-motion');
    if (!wall || !data || !control) return;

    const pool = JSON.parse(data.textContent);
    const tiles = [...wall.querySelectorAll('.cake-tile')];
    const reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)');
    const preferenceKey = 'csc-wall-motion';
    let savedPreference = null;
    try {
        savedPreference = window.sessionStorage.getItem(preferenceKey);
    } catch (error) {
        console.info('Animation preferences cannot be saved in this browser.', error);
    }
    let chosen = savedPreference === 'on' || savedPreference === 'off';
    let paused = chosen ? savedPreference === 'off' : reducedMotion.matches;
    let busy = false;

    function syncMotion() {
        wall.classList.toggle('motion-paused', paused);
        wall.classList.toggle('motion-enabled', !paused);
        control.hidden = false;
        control.textContent = paused ? 'Enable animation' : 'Pause animation';
        control.setAttribute('aria-pressed', String(paused));
    }

    control.addEventListener('click', () => {
        paused = !paused;
        chosen = true;
        try {
            window.sessionStorage.setItem(preferenceKey, paused ? 'off' : 'on');
        } catch (error) {
            console.info('Animation preference will reset when the page reloads.', error);
        }
        syncMotion();
    });
    reducedMotion.addEventListener('change', () => {
        if (!chosen) paused = reducedMotion.matches;
        syncMotion();
    });
    syncMotion();

    window.setInterval(async () => {
        if (busy || paused || document.hidden) return;
        const visible = tiles.filter(tile => tile.getClientRects().length);
        const used = new Set(tiles.map(tile => tile.querySelector('img').getAttribute('src')));
        const candidates = pool.filter(source => !used.has(source));
        if (!visible.length || !candidates.length) return;
        const tile = visible[Math.floor(Math.random() * visible.length)];
        const source = candidates[Math.floor(Math.random() * candidates.length)];
        const image = tile.querySelector('img');
        const next = new Image();
        busy = true;
        try {
            next.src = source;
            await next.decode();
            if (paused || document.hidden) return;
            tile.classList.add('fading');
            await new Promise(resolve => window.setTimeout(resolve, 650));
            if (!paused && !document.hidden) image.src = source;
        } catch (error) {
            console.warn('Could not load a cake photograph.', error);
        } finally {
            tile.classList.remove('fading');
            busy = false;
        }
    }, 2600);
})();
