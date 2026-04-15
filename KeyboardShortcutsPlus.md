if (!window.ksPlusPressedKeys) {
    window.ksPlusPressedKeys = new Set();
}

if (window.ksPlusActive) {
    window.removeEventListener('keydown', window.ksPlusActive, true);
    window.removeEventListener('keyup', window.ksPlusUp, true);
}

window.ksPlusUp = (e) => {
    window.ksPlusPressedKeys.delete(e.code);
};

window.ksPlusActive = async (e) => {
    window.ksPlusPressedKeys.add(e.code);

    const active = document.activeElement;
    if (active && (active.tagName === 'TEXTAREA' || active.tagName === 'INPUT' || active.isContentEditable || active.closest('.excalidraw-textEditorContainer'))) return;

    const leaf = app.workspace.activeLeaf;
    if (!leaf || !leaf.view || leaf.view.getViewType() !== "excalidraw") return;

    const ea = typeof ExcalidrawAutomate !== 'undefined' ? ExcalidrawAutomate : window.ExcalidrawAutomate;
    if (!ea) return;
    ea.setView(leaf.view);

    const isMac = /Mac|iPod|iPhone|iPad/.test(navigator.platform);
    const mod = isMac ? e.metaKey : e.ctrlKey;
    const opt = e.altKey;
    const shf = e.shiftKey;
    const clean = (s) => s ? s.replace(/\s*\^[\w\-]+$/, "") : s;

    if (e.code.startsWith('Arrow') && !mod && !opt) {
        const activeArrows = ['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'].filter(k => window.ksPlusPressedKeys.has(k));
        if (activeArrows.length > 1) {
            const els = ea.getViewSelectedElements();
            if (els.length > 0) {
                e.preventDefault(); e.stopPropagation();
                let dx = 0; let dy = 0;
                const d = shf ? 20 : 12;
                if (window.ksPlusPressedKeys.has('ArrowUp')) dy -= d;
                if (window.ksPlusPressedKeys.has('ArrowDown')) dy += d;
                if (window.ksPlusPressedKeys.has('ArrowLeft')) dx -= d;
                if (window.ksPlusPressedKeys.has('ArrowRight')) dx += d;
                ea.clear(); ea.copyViewElementsToEAforEditing(els);
                for (let el of ea.getElements()) { el.x += dx; el.y += dy; }
                await ea.addElementsToView(false, false);
                return;
            }
        }
    }

    if (mod && shf && (e.code === 'KeyL' || e.code === 'Semicolon' || e.code === 'Quote')) {
        const els = ea.getViewSelectedElements();
        if (els.length === 0) return;
        e.preventDefault(); e.stopPropagation();
        let a = e.code === 'KeyL' ? "left" : (e.code === 'Quote' ? "right" : "center");
        ea.clear(); ea.copyViewElementsToEAforEditing(els);
        const viewEls = ea.getViewElements();
        for (let el of ea.getElements()) {
            if (el.type === 'text') el.textAlign = a;
            else {
                const t = viewEls.find(txt => txt.type === 'text' && txt.containerId === el.id);
                if (t) {
                    ea.copyViewElementsToEAforEditing([t]);
                    const et = ea.getElements().find(x => x.id === t.id);
                    if (et) et.textAlign = a;
                }
            }
        }
        await ea.addElementsToView(false, false);
    }

    if (mod && !opt && !shf && e.code.startsWith('Arrow')) {
        const els = ea.getViewSelectedElements();
        if (els.length === 0) return;
        e.preventDefault(); e.stopPropagation();
        let dx = 0, dy = 0;
        switch (e.code) { case 'ArrowUp': dy = -1; break; case 'ArrowDown': dy = 1; break; case 'ArrowLeft': dx = -1; break; case 'ArrowRight': dx = 1; break; }
        ea.clear(); const ids = []; ea.copyViewElementsToEAforEditing(els);
        for (let el of ea.getElements()) {
            if (!['rectangle', 'ellipse', 'diamond', 'image', 'text'].includes(el.type)) continue;
            if (el.type === 'text' && el.containerId && els.some(e => e.id === el.containerId)) continue;
            const c = JSON.parse(JSON.stringify(el));
            c.id = "id_" + Date.now() + Math.random().toString(16).slice(2);
            c.x += dx * (el.width + 80); c.y += dy * (el.height + 80);
            c.boundElements = []; c.backgroundColor = "transparent";
            ea.elementsDict[c.id] = c; ids.push(c.id);
            const aid = ea.addArrow([[el.x + el.width/2, el.y + el.height/2], [c.x + c.width/2, c.y + c.height/2]], { strokeColor: el.strokeColor, strokeWidth: 1, endArrowhead: "arrow", roundness: { type: 2 } });
            const arr = ea.elementsDict[aid]; arr.startBinding = { elementId: el.id }; arr.endBinding = { elementId: c.id };
            el.boundElements = [...(el.boundElements || []), { id: aid, type: "arrow" }];
            c.boundElements = [...(c.boundElements || []), { id: aid, type: "arrow" }];
        }
        await ea.addElementsToView(false, false);
        ea.selectElementsInView(ea.getViewElements().filter(el => ids.includes(el.id)));
    }

    const isK = e.code === 'Comma' || e.code === 'Period';
    if (isK && (shf || opt)) {
        const els = ea.getViewSelectedElements();
        if (els.length === 0 || (mod && shf && els.every(el => el.type === 'text'))) return;
        e.preventDefault(); e.stopPropagation();
        ea.clear(); ea.copyViewElementsToEAforEditing(els);
        const s = e.code === 'Period' ? 1.1 : 0.9;
        const sw = e.code === 'Period' ? 1 : -1;
        for (let el of ea.getElements()) {
            if ((el.type === 'arrow' || el.type === 'line') && mod && shf) {
                el.strokeWidth = Math.max(1, (el.strokeWidth || 1) + sw);
            } else if (el.type !== 'text') {
                if (mod && shf) {
                    const nW = el.width * s; const nH = el.height * s;
                    el.x -= (nW - el.width)/2; el.y -= (nH - el.height)/2;
                    el.width = nW; el.height = nH;
                } else if (shf) {
                    const nW = el.width * s; el.x -= (nW - el.width)/2; el.width = nW;
                } else if (opt) {
                    const nH = el.height * s; el.y -= (nH - el.height)/2; el.height = nH;
                }
            }
        }
        await ea.addElementsToView(false, false);
    }

    if (opt && !mod && e.code === 'KeyF') {
        const els = ea.getViewSelectedElements();
        if (els.length === 0) return;
        e.preventDefault(); e.stopPropagation();
        ea.clear(); ea.copyViewElementsToEAforEditing(els);
        for (let el of ea.getElements()) {
            if (!['rectangle', 'ellipse', 'diamond'].includes(el.type)) continue;
            if (el.backgroundColor === 'transparent') { el.backgroundColor = '#fff3bf'; el.fillStyle = 'hachure'; }
            else {
                const st = ['hachure', 'cross-hatch', 'solid', 'transparent'];
                let i = st.indexOf(el.fillStyle);
                el.fillStyle = st[(i + 1) % st.length];
                if (el.fillStyle === 'transparent') el.backgroundColor = 'transparent';
            }
        }
        await ea.addElementsToView(false, false);
    }

    if (mod && !shf && e.code === 'Backquote') {
        const els = ea.getViewSelectedElements();
        if (els.length === 0) return;
        e.preventDefault(); e.stopPropagation();
        ea.clear(); ea.copyViewElementsToEAforEditing(els);
        const vEls = ea.getViewElements();
        for (let el of ea.getElements()) {
            el.strokeColor = "#000000"; el.backgroundColor = "transparent";
            if (el.type === 'text') { el.text = clean(el.text); el.rawText = clean(el.rawText); }
            else {
                const t = vEls.find(tx => tx.type === 'text' && tx.containerId === el.id);
                if (t) t.strokeColor = "#000000";
            }
        }
        await ea.addElementsToView(false, false);
    }

    if (opt && !mod && e.code.startsWith('Digit')) {
        const d = parseInt(e.code.replace('Digit', ''));
        if (d >= 1 && d <= 8) {
            e.preventDefault(); e.stopPropagation();
            const p = ['#ffc9c9', '#ffe8cc', '#fff3bf', '#d8f5a2', '#a5d8ff', '#d0bfff', '#fcc2d7', '#e9ecef'];
            const sa = ['#c92a2a', '#e67700', '#f59f00', '#2b8a3e', '#1864ab', '#6741d9', '#a61e4d', '#495057'];
            ea.clear(); ea.copyViewElementsToEAforEditing(ea.getViewSelectedElements());
            for (let el of ea.getElements()) {
                if (el.type === 'text') el.strokeColor = sa[d-1];
                else { el.backgroundColor = p[d-1]; if (el.fillStyle === 'transparent') el.fillStyle = 'hachure'; }
            }
            await ea.addElementsToView(false, false);
        }
    }

    if (mod && !shf && e.code === 'Slash') {
        e.preventDefault(); e.stopPropagation();
        ea.clear(); ea.copyViewElementsToEAforEditing(ea.getViewSelectedElements());
        const v = ea.getViewElements();
        for (let el of ea.getElements()) {
            const t = v.find(tx => tx.type === 'text' && tx.containerId === el.id);
            if (t) {
                const nW = t.width + 30; const nH = t.height + 30;
                el.x += (el.width - nW)/2; el.y += (el.height - nH)/2;
                el.width = nW; el.height = nH;
            }
        }
        await ea.addElementsToView(false, false);
    }
};

window.addEventListener('keydown', window.ksPlusActive, true);
window.addEventListener('keyup', window.ksPlusUp, true);
new Notice("KeyboardShortcutsPlus Active");
