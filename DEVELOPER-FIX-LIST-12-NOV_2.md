# Cairn: what to fix (from the review of the running app, 12 Nov)

Source: I opened localhost:3000 page by page (Files, Ask, Quizzes, Tales, Library, Classroom, Study, Family, Usage, Settings, Feature flags and Kids), read computed styles and widths, and checked the sidebar fold. Design boards are in the Cairn design canvas. Details and measurements: sections 30, 31 and 32 of DEVELOPER-UPDATE-12-NOV-B.md; Hoot animation: section 28.

How to check any item: open the page at window widths 800, 1280, 1920 and 2880, with the sidebar open and folded.

## Priority 1: layout (affects every page)

**F1. One shell, one content width.** Today the page content is 960 px on Ask, Library, Classroom, Tales and Family; 1116 on Usage; 1480 on Feature flags; 1689 on Quizzes; 1910 on Study; 2043 on Files; 2480 on Settings (measured at 2880 px, sidebar open). Each page sets its own `max-width`.
Do: remove page-level max-widths. Shell = top bar 56 px, sidebar 240 px (72 folded), content. Content = window minus sidebar minus 24 px gutters (minus tips panel when shown), capped once at 2200 px and then centred.
Done when: every page has the same content width at the same window size.

**F2. Sidebar fold must stretch the content.** Folding only slides the content (Files stayed 2043 px wide, moved left).
Do: content width recalculates on fold (0.2 s), cards re-flow into more columns.
Done when: folding at 1920 px adds about 168 px to the content on every page.

**F3. Card lists reflow.** Use `grid-template-columns: repeat(auto-fill, minmax(340px, 1fr))` for lists of cards (Tales, Library, Classroom, stat cards, Settings). Reading text keeps a 72 character line.

**F4. Tips panel ("Good to know") is one shell part.** Today it takes a column on Files, Quizzes, Study and Settings only. Do: 300 px, sticky right, on every page or none; under 1280 px it becomes an info icon in the page header.

**F5. Settings layout is broken.** Three fixed 644 px columns, each row as tall as its tallest card. The Appearance card is about 2400 px tall, so Profile and Verification sit over empty space, and so does the next row (Notifications, Family links, Answer model).
Do: one list of folds (or two independent stacks, never a row grid), section chips as an anchor bar, all folds closed except the one in the URL hash. The "Your look" preview must not show the wallpaper behind its text and buttons (give it its own surface).
Done when: no empty gaps larger than 24 px between cards at any width.

**F6. Wallpaper strip beside the sidebar.** About 16 px of wallpaper shows between the sidebar and the content on every page and cuts pictures in half. Do: the content area starts at the sidebar edge.

**F7. Dev inbox button** (bottom right) covers content on every page and in Kids mode. Do: hide it outside development, or make it a small icon that does not cover content.

**F8. "Good to know" popup has a fixed height (seen in Glass theme, narrow window).** The popup is tall with big empty gaps: between the title and the first tip, between the tips, before "Never show these tips", and a large empty area under the Close button. The height is fixed (or the content is spread to fill it), so it never fits its content.
Do: height from content (`height: auto`, no fixed or `min-height`, no `flex: 1` or `justify-content: space-between` on the body), `max-height: 80vh` with the tips list scrolling inside if it is longer. Spacing: title to list 12 px, between tips 8 px, list to checkbox 16 px, checkbox to bottom padding 24 px. Width 320 to 360 px.
Also: the popup has an X in the corner and a text Close button; keep only the X (icon button with aria-label "Close"). Keep "Never show these tips" as a checkbox row with the label to its right.
Done when: with 2 tips the popup is about 230 px tall, with 6 tips it grows, and it never shows empty space bigger than 24 px.

**F9. Ask: the "Choose files" panel opens upward and is cut off at the top.** In your screenshot the panel grows above the files bar, goes past the top of the window, and the first files and the header are not reachable. The "Done" button and the list are the only visible parts. Also: each row shows the long text "Use Insects Guide in answers" next to the file name (the file name is already there), and there is about 150 px of empty space between the files bar and the question input.
Do:
1. The panel gets `max-height: min(70vh, 520px)` and is a flex column: header with count and All / None, search, the list (`flex: 1; min-height: 80px; overflow: auto`) and the Done button, which always stays visible at the bottom.
2. If there is less than 320 px free above the bar (short window or page scrolled), open it downward instead, over the input area, or as a bottom sheet fixed to the window on phones. It must never go outside the window.
3. Each row: type chip, file name, one line of detail, and the switch. The text "Use X in answers" is only the `aria-label` of the switch, not visible.
4. Remove the empty gap: the files bar sits directly above the input (12 px), the input stays at the bottom of the page.
5. "Done" can also be an icon button (check mark) per the icon-button rule; Escape and a click outside close the panel.
Done when: with 5 files and a window 600 px tall the whole panel is visible and the list scrolls inside it.

**F10. Vertical coloured lines in Settings (dependent fields).** In Settings, every group that appears when a switch is on (for example "Use my own colour" with its colour code, "Send me notifications" and its channels, the Kids mode rules) has a 2 px accent-coloured line on its left edge (`.dep`, `border-left: 1.8 px rgb(185,164,255)`). It is not part of the design, it clashes with the rounded cards and the accent changes its colour with the theme.
Do: remove `border-left` from `.dep`. Show a dependent group as a tinted inner card instead: `background: var(--surface2)`, `border-radius: 16px`, `padding: 16px`, no left margin, 12 px gap between its fields (the same inner-card rule as in section 30). When the switch is off the group is hidden, as today.
Also look for the same pattern elsewhere and replace it the same way: Study (open card), the answer comment box and Compare hits/misses use 3 px coloured left lines in the design files; in the app search for `border-left` with an accent colour.
Done when: no element in Settings has a single coloured side border.

**F11. Every dialog needs a close X at the top right** (section 37). Missing today on: Start Kids mode, Unlock for grown-ups (PIN), delete confirms (file, tale, student, account), Good to know popup and more. Fix: one shared Modal component with an icon button (aria-label "Close", 44 px target) at top 12 / right 12; keep Cancel in the footer; Escape and click on the scrim also close; title gets 44 px right padding. Destructive dialogs: closing = Cancel.

## Priority 2: Kids mode is not built as designed

Today (/kids): one 400 px column, small question box with an empty gap below, a one-line owl card that does not move or talk, a stars card, the break-time quiz as a big card in the page flow, two small shortcut buttons. There is no chat.
Design: board "Kids home v2 (buddy chat, quiz card)".

**K1. Kids home layout.** No sidebar. Top bar: logo, Kids tag, one star pill (tap for stars, badge, reward in a popup), lock icon for the PIN (replace the small "Grown-up" text). Body: buddy stage (300 to 420 px, left) and chat (all the rest). On a phone the buddy is a header strip above the chat. Everything stretches with the window.

**K2. Chat.** Thread with child bubbles right (accent colour) and buddy bubbles left, 20 px text, 60 px input, big round Speak and Ask icon buttons, three suggested questions from the child's files. The answer appears next to the question (today there is no thread).

**K3. Buddy.** Big (300 px), with a speech bubble. States follow the chat: idle (blinks), thinking while the answer is found, talking while read aloud, cheer after a star. Reduce motion = still poses. Poses and timings: section 28. Only Hoot has a full set of SVG poses (zip delivered). Fox and monkey can be converted the same way; lion, panda, orca and meerkat need new generated sets. Until a buddy has a set, show its still picture.

**K4. Quiz card.** Slides in from the right (0.5 s) when a quiz is due. Two choices: Let's play, or X (Not now). Not now docks the card as a "Quiz" tab on the right edge; after the wait the grown-up sets (default 10 minutes) it comes back as a card under the last chat message. It never blocks the input. "Quiz first, then reading" stays a full-screen card, the only blocking case.

**K5. Tales and My room** become two big icon buttons under the buddy.

## Priority 3: titles, labels, spacing (every card)

**T1. Card titles use four styles only** (section 30): h2 card title 20/500 Host Grotesk; h3 item title 16/600; h4 note title 15/600; mono 11 px uppercase label for field and stat names only. Fix: Quizzes titles ("Quiz details", "When it plays", "Questions") are mono uppercase 22 px; "Good to know" is h2 16/500; "Your look" and the blocked-detail note are 15/700; "Q1..Q3" are plain legends.

**T2. Spacing on an 8 px grid.** Outer card 24 px padding and 26 px corner; inner card 16 px and 16 px corner; title to subtitle 4 px; title to content 12 px; label to control 8 px (today 5, 10 and 15 px); between cards 24 px. Today card padding is 24, 16, 12x16, 32x16 and 4; corners 26, 16 and 14.

**T3. Build four components:** CardTitle, ItemTitle, NoteTitle, FieldLabel, plus one Card with a size prop. Remove one-off title styles.

## Priority 4: page by page

- **Ask:** a 700 px card in the middle, small files bar, chips and suggestions. Use the shell width; keep the input pinned at the bottom (design in section on the Ask files bar).
- **Classroom:** card inside a card inside a card. Flatten to one card per class.
- **Tales:** the stars card is long and pushes the tale cards down; make it a compact strip.
- **Usage:** five stat cards in a 3 + 2 grid with an empty slot. Use the auto-fill grid so no slot is empty, or a 5-column row on wide screens.
- **Family:** an orphan card under the left column and an empty right. Use the grid.
- **Feature flags:** the table is narrower than its card, with a large gap between columns. Make the table fill the card.
- **Breadcrumbs:** shown on Library, Family, Usage, Feature flags; missing on same-level pages. Rule: breadcrumb and back button only on nested pages (not on top-level pages).

## Designed earlier, check status in the app

Not verified by me in the running app, please compare with the boards and sections 1 to 29 of DEVELOPER-UPDATE-12-NOV-B.md: mode switch pill and account menu (ChromeBar), avatar editor (animals, colours, letter), answer reactions, tale editor with saved cards, Ask files bar and picker, wallpapers (23, one subject each, with reading surface so menus stay readable), Class students join code.

## Notes
- The test tale "QA bug hunt tale" still exists in the Tales list; please remove it by hand.
- I unlocked Kids mode with the test PIN to review the grown-up pages and turned Kids mode back on afterwards.

## Status check of the running app, 13 Nov (signed in as admin, adult mode, 1437 px window)

Checked: Files, Ask, Quizzes, Tales, Library, Classroom, Study, Family, Usage, Settings, 404. Not checked: Kids mode (/kids opens the grown-up view in this session), dialogs, quiz from tales (not built yet).

**Better than before:** mode switch is a compact 3-icon pill; Settings sections fold and show their value when closed; Usage has five equal stat cards; 404 page is friendly; the Good to know popup closes into a small "i" button.

**S1. Good to know popup still covers content on every page.** It sits bottom right and hides: the Write a new tale button, the Quiz details fields, the end of the "Why no leaderboard" card, the Usage chart legend, the Classroom "Start class" row. Fix: do not float over content. Use the free right column (rail) on wide screens, and on narrow screens start collapsed as the "i" button. Show the full tip once per page, then only the "i".
**S2. Tales: "Got an idea?" card has a big empty gap** between the name field and the button (fixed height). Let the card height follow content; put the field and a round "write" icon button on one row.
**S3. Classroom: cards do not use the width.** "1A" class card is a third of the width, the rest is empty; the "New" card has large gaps and a tiny Class name field. Use the shell grid (auto-fill cards), full-width field, and one card per class.
**S4. Ask: empty state is bare.** Big empty area, input floating in the middle. Pin the input to the bottom, show the buddy or three suggestion cards in the empty state, and keep chips above the input.
**S5. Library: "No tales here yet." is plain text.** Make an empty state: small illustration, one line of why, and an icon button to share a tale.
**S6. Files: failed file has no way out.** "Old school report (scan).jpg" shows Failed only. Add the reason in one line and two icon buttons: Try again, Remove. The "faktura" file shows "Very little text": offer "Make a text version" help link.
**S7. Unnamed "✉ 0" chip at bottom right** (under the popup). Name it with a tooltip, or move it into the bell menu.
**S8. Family: "My week" chart** is mostly empty bars with tiny labels; show a short sentence under it ("Best day: Thu") and give the chart a minimum height so labels stay readable.
**S9. Study: empty state is a plain numbered list.** Turn it into a 3-step strip with icons (Pick a book, Choose how many, Write the cards) and mark the current step.
**S10. 404 page button says "Back to your documents".** Use "Back to Files" to match the sidebar name.

## Kids mode check, 13 Nov (played through in the running app)

**Done well now:** big owl on the left with a speech bubble, large chat on the right, suggestion chips, Tales and My room buttons under the owl, stars in the top bar, lock icon. The quiz is a skippable card that folds to a small "Quiz" tab on the right edge. The owl cheers at the end of the quiz. Read-aloud and reaction buttons (😀 👍 🤔) are under the answer. The Start Kids mode and Unlock dialogs now have the top-right X (so F11 is done for those two; the others are still to check).

**K6. Answers show grown-up controls and red errors to the child.** Every source card has Copy, Show full page and Open file at page N, and under each one a red line "This is locked in kids mode. Ask a grown-up." A child should never see locked buttons or errors. Fix: in Kids mode hide those controls completely. Show sources as small picture chips ("From The Little Fox, page 2") that open a read-only large-text card. Keep the kid answer to 3 short sentences; sources collapsed by default.
**K7. Stray pictures at the top left of every Kids page.** Clipped small sprites sit behind the card corner and, on Tales and My room, behind the page title. Looks like the old buddy or wallpaper items rendered outside their box. Remove, or render the wallpaper as the page background only.
**K8. Quiz card covers the chat.** It floats over the answer and the input row. Fix: dock it in the right rail on wide screens; on small screens a bottom sheet that never covers the input. It must be easy to skip (X plus "Later").
**K9. Quiz gives no feedback after each answer.** Tapping an answer jumps straight to the next question: no correct or wrong sound or colour, no owl reaction between questions. Wrong answer: owl says "Nice try!" and shows the right answer for 1.5 s. Right answer: star pops, owl cheers. Owl bubble stays "We know so much!" the whole time; it should change with each event.
**K10. Quiz result screen is tiny and unclear.** "You got 2 of 3" is small text; all three answers show in green even though one was wrong. Show big stars, mark the missed question in a different colour with the right answer, and two big icon buttons: Play again, Done.
**K11. Tales and My room use the grown-up layout inside a card.** No owl, tiny "Home" link, the same empty gap in "Got an idea?". Give Kids pages a big round back button with the owl, large type, and the tale cards with pictures. The sub-pages should keep the buddy in the corner.
**K12. My room: the wallpaper grid is cut off** by an inner scroll box (third row is clipped), and there is no preview or avatar/buddy editing. Remove the nested scroll, add a live preview at the top, and add the avatar editor (animal, colour) from section 29.
**K13. Chat input is small for a "big chat".** The chat uses about 60% of the width and the answer is a boxed card. Make messages big speech bubbles, owl on the left, input pinned bottom with a big mic and send icon button.

## Mobile check, 13 Nov (375 x 667 window, grown-up and Kids)

**Fine on mobile:** no sideways scrolling on any page; the top bar fits (logo icon, mode pill, bell, avatar, menu); the menu opens as a list; cards stack full width; buttons in lists are full width and easy to tap; Files list rows are clear; Settings chips wrap.

**M1. Good to know popup covers about half of the screen on every page** (worst case of S1). On a phone it must never open by itself. Show only the small "i" button; open as a bottom sheet when tapped, with a grab handle and X.
**M2. Three floating round buttons crowd the bottom right** ("i" tips, the unnamed "✉ 0" chip, and the dev button). The "i" sits on top of content, for example it covers the Classroom "Open" button and list actions. Fix: one floating button at most; give pages 72 px bottom padding; remove the "✉ 0" chip or move it into the bell. Hide the dev button outside development builds.
**M3. Files: the detail panel is at the very bottom** of the page, after all the files and the drop box. Picking a file shows nothing. Open the details as a full-screen sheet (with back button and X) when a file is tapped.
**M4. Ask: file chips stack as a tall list** (each chip full row, long names cut: "faktura[..."), pushing the input down, the third chip is clipped by an inner scroll box, and the input is not pinned. Fix: one horizontal scrolling chip row (or just a "5 files" chip that opens the picker sheet); pin the input and the Ask icon button to the bottom above the keyboard.
**M5. Usage: five stat cards in one column**, each about 120 px tall, so the chart is far down. Use a 2-column grid for the small stats (cost and tokens full width, then questions / average / time in pairs).
**M6. Classroom: Class name field is only half the width** and Start class sits below it with a gap. Make the field full width with the icon button inside the row; "New" card inside card inside card wastes 40 px on each side, so flatten.
**M7. Family tabs wrap into a two-row pill** with a rounded box that looks broken. Use a single scrolling row of tabs or a short dropdown.
**M8. Settings: nine jump chips use three rows** before the first section. Use one horizontally scrolling row.
**M9. Touch targets under 44 px:** the checkbox in the popup (22 px) and the dev inbox button (37 px). Everything else measured at 44 px or more.
**M10. Kids mode on phone: the owl is about 50 px** next to the speech bubble, so the "big buddy" idea is lost. Keep the owl at least 40% of screen width above the chat on first screen. Once a conversation starts, the owl scrolls away and never comes back. Keep a small owl pinned top-left that reacts to answers.
**M11. Kids quiz card covers the chat input and the suggestions** on first load. On phone, the quiz should be only the small "Quiz" tab on the right edge until the child taps it, then a bottom sheet.
**M12. Kids answer cards: the locked grown-up buttons wrap into a messy stack** (Copy / Show full / page / Open file / at page 3) plus the red line. Same fix as K6: remove them for kids.
**M13. Kids chat input is small and clipped** ("What would you l|") with a round send button; make the input two lines high with a big mic button next to it.

## Kids v3 decision, 13 Nov

**K14. Rebuild Kids home as in the board "Kids v3" (section 42).** Replaces the big-owl stage: classic chat with a small buddy face on every reply, level ring with the buddy face in the top bar, quiz joystick with a notification badge, big emoji menu cards on the left (bottom tabs on phone), child avatar next to the child's messages. Still needed from earlier: K6 (no locked buttons), K9 (feedback after each quiz answer), K10 (result screen), K12 (My room scroll box).

**K15. Add the buddy shop (section 43).** Bag button in the Kids top bar, shop panel (sheet on phone) with Glasses, Hats, Colours, Pins, try-before-buy, spendable star balance separate from level progress, grown-up switch and weekly limit in Settings, Kids mode. Menu cards in Kids v3 are square, 2 per row, and My room uses the palette icon.

**K16. Kids settings for mic and uploads (section 44).** New Settings fold with three switches (microphone, file uploads with approval and size, buddy shop with weekly limit). Kids bar shows the paperclip and mic only when allowed. Enforce on the server. Default for Young reader: uploads off.

**K17. Shop notification (section 47).** Gold count badge on the bag button, a 6-second toast "You can buy X!", "New for you" banner, gold "New" tags and section dots inside the shop. Track "seen" item ids per child; no badge when the weekly limit is reached or when the shop is off.


### K18 – Hoot invites and phrase bank (from section 48)
Add `buddy_phrases` data, the invite card with the rules in 48.2 (max 1 per 20 min, none in quiz / answer / quiet hours, 2 Not nows = stop for the day) and the Settings switch "Hoot invites to play".

### K19 – Slow and failed answer states (section 49)
Slow hint after 8 s, streamed text, friendly failure row with retry icon, no codes shown to the child.

### K20–K29 – From the three reviews (section 50, KIDS-UX-THREE-VIEWS-13-NOV.md)
- K20: Read-aloud on by default, moving face
- K21: Mic first for Young reader
- K22: Tap-the-owl giggle and phrase
- K23: Star-fill progress and star icons in prices
- K24: Friendly PIN dialog ('Ask a grown-up')
- K25: Class login by code or picture
- K26: Teacher Pause all and whiteboard mode
- K27: Czech, Ukrainian, English
- K28: Sleeping Hoot at quiet hours and time limit
- K29: 'Say it simpler' button and reading level per child

### Status of K20–K29 (13 Nov)
All ten now have a design: K20–K24, K28, K29 on the Kids v3 board; K25–K27 on the Kids in class board; K26 (languages) also in the class board. Build order is in DEVELOPER-PRIORITY-PLAN-13-NOV.md.

### Dialog audit, 13 Nov (running app)
- Settings → Kids mode: the pencil buttons open inline editors (the pencil turns into an X). No modal, nothing to fix.
- Tales list: no delete button on the list (delete is inside the tale editor).
- Still not checked by me on purpose: the delete-account confirmation (Settings → Danger zone, folded) and the delete-tale confirmation. Developer: please confirm both have an X and Cancel / Delete space-between like the other dialogs.
