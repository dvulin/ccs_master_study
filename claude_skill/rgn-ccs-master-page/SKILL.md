---
name: rgn-ccs-master-page
description: Use when an RGN web administrator puts or updates the CO2 Geological Storage master (CCS master) page on www.rgn.unizg.hr from GitHub repo dvulin/ccs_master_study.
---

# CCS master page on the RGN Joomla site

This skill helps the RGN web administrator publish and update the page of the International Master in CO2 Geological Storage (joint master of RGN Zagreb and Sapienza Rome) on the faculty's Joomla site. All content and files come from the programme coordinator's public GitHub repository. The administrator only pastes one HTML block into one Joomla article.

The programme coordinator owns the content: Domagoj Vulin, domagoj.vulin@rgn.unizg.hr. Never rewrite, shorten, translate or "improve" the page text, dates, fee or e-mail addresses. If something looks wrong, report it to the coordinator instead of changing it.

## Where everything is

| What | Address |
|---|---|
| Repository | https://github.com/dvulin/ccs_master_study |
| HTML block to paste into the article (plain text) | https://raw.githubusercontent.com/dvulin/ccs_master_study/main/CCS_master_page.html |
| How the page should look | https://dvulin.github.io/ccs_master_study/ |
| Brochure (PDF) | https://dvulin.github.io/ccs_master_study/CCS_master_brochure_2027.pdf |
| Leaflet (PDF) | https://dvulin.github.io/ccs_master_study/CCS_master_leaflet_2027.pdf |
| Pictures | https://github.com/dvulin/ccs_master_study/tree/main/img (served from https://dvulin.github.io/ccs_master_study/img/...) |
| This skill | https://github.com/dvulin/ccs_master_study/tree/main/claude_skill |

Page addresses on the RGN site:
- Main: https://www.rgn.unizg.hr/en/ccs-master
- https://www.rgn.unizg.hr/hr/ccs-master shows the same English page or redirects to /en/ccs-master (there is no Croatian version).
- The old address /en/studies/postgraduate-study/co2-geological-storage redirects to /en/ccs-master.

Nothing is uploaded to the RGN server. Pictures and PDFs load directly from GitHub, so when the coordinator replaces them on GitHub the RGN page shows the new files without any action. Only a change of the page text needs the block to be pasted again.

## Step 1: Get and check the current block

Download the block with a shell command, not with a web page summariser (the text must be exact):

```bash
mkdir -p ccs && cd ccs
curl -fsSL https://raw.githubusercontent.com/dvulin/ccs_master_study/main/CCS_master_page.html -o CCS_master_page.html
```

Then run these checks and report each result to the administrator:

```bash
python3 - <<'PY'
import re, urllib.request, urllib.error
s = open("CCS_master_page.html", encoding="utf-8").read()
ok = True
def check(cond, msg):
    global ok; ok &= cond; print(("OK   " if cond else "FAIL ") + msg)
check("START of the block" in s and "END of the block" in s, "START and END markers present")
check(not re.search(r"<(script|style|link|iframe|form|object|embed)\b", s, re.I), "no script/style/link/iframe/form/object/embed tags")
check("domagoj.vulin@rgn.unizg.hr" in s and "sabina.bigi@uniroma1.it" in s, "both contact e-mails present")
assets = sorted(set(re.findall(r'(?:src|href)="(https://dvulin\.github\.io/ccs_master_study/[^"]+)"', s)))
unreachable = []
for u in assets:
    try:
        code = urllib.request.urlopen(urllib.request.Request(u, method="HEAD"), timeout=20).status
    except urllib.error.HTTPError as e:
        code = e.code                      # GitHub answered: a real error such as 404
    except Exception:
        unreachable.append(u); continue    # this environment cannot reach GitHub Pages
    check(code == 200, f"{u} -> {code}")
for u in unreachable:
    print("SKIP " + u + "  (not reachable from here: check it in a browser)")
other = [u for u in re.findall(r'src="([^"]+)"', s) if not u.startswith("https://dvulin.github.io/ccs_master_study/")]
check(not other, "all pictures load from the coordinator's GitHub Pages" + ("" if not other else f" (other: {other})"))
print("\nDO NOT PASTE YET - report the FAIL lines to domagoj.vulin@rgn.unizg.hr" if not ok else
      "\nREADY TO PASTE" + (" (after the SKIP links open in a browser)" if unreachable else ""))
PY
```

If the environment cannot reach GitHub, ask the administrator to open the raw address above in a browser, save the page as CCS_master_page.html and attach it; then run the same checks on the attached file (the link checks will be skipped).

If a picture or PDF returns 404, GitHub Pages is probably not published yet or is still building. Do not paste; tell the coordinator.

If links are marked SKIP, your environment cannot reach GitHub Pages (a network limit, not an error in the page). Ask the administrator to open https://dvulin.github.io/ccs_master_study/ in a browser and confirm that the pictures show and both PDF buttons open; that replaces the link check.

If the administrator pastes the HTML currently in the Joomla article, compare it with the new block (a plain diff is enough). Summarise what changed in a few lines so the administrator knows what they are publishing.

## Step 2: Hand the block to the administrator

Give the administrator the complete file CCS_master_page.html, from the START comment to the END comment, unchanged. Do not reformat, pretty-print or re-encode it. Do not paste a shortened excerpt into the chat as "the block".

## Step 3: Publish in Joomla (administrator's steps)

Guide the administrator through these steps:

1. Open the CCS master article (or create it, language English or All, with menu items or redirects for the addresses above).
2. Switch the editor to code view: "Toggle Editor" or the <> source button. Never paste into the visual editor.
3. Select all existing content in the article and replace it with the whole block. Save.
4. The block uses only normal HTML with style="..." attributes. If the page looks plain after saving (no colours, no rounded boxes), Joomla's text filter removed the style attributes. Save as a user whose group has "No Filtering" in System > Global Configuration > Text Filters, or ask the site's super user to save it.
5. Redirects, once: in System > Redirects, add
   - /en/studies/postgraduate-study/co2-geological-storage -> /en/ccs-master
   - /hr/ccs-master -> /en/ccs-master (unless /hr/ccs-master is a menu item showing the same article)
6. Clear the Joomla cache if the site uses page caching.

## Step 4: Check the live page

Ask the administrator to open https://www.rgn.unizg.hr/en/ccs-master on a computer and on a phone, and confirm:
- the cover picture and the 2027 timeline are visible;
- the Brochure and Leaflet buttons open the two PDFs;
- the section buttons (The master, Getting in, ...) jump to the right section;
- the three "Join the list" buttons open a pre-written e-mail to domagoj.vulin@rgn.unizg.hr (e-mail app, Gmail, Outlook.com);
- the contact e-mail links work.

The section buttons point to https://www.rgn.unizg.hr/en/ccs-master#... . If the page is published at a different address, tell the coordinator; do not edit the links yourself.

## Troubleshooting

| Symptom | Likely cause | What to do |
|---|---|---|
| Page is plain text, no colours | style attributes stripped by the text filter or the editor | Save with "No Filtering", paste in code view |
| Pictures missing, PDF buttons give 404 | GitHub Pages not published or temporarily down | Check https://dvulin.github.io/ccs_master_study/ ; tell the coordinator |
| Layout broken in places | Editor rewrote the HTML on save | Paste again with the editor switched off (Toggle Editor) |
| Old content still shows | Joomla page cache or browser cache | Clear the Joomla cache; reload with Ctrl+F5 |
| Visitors reach the old page | Redirect missing | Add it in System > Redirects |

## Rules

- Content changes come only from the coordinator through GitHub. Never edit the text in Joomla.
- Do not add scripts, trackers, iframes or extensions for this page unless the coordinator asks for them.
- Do not copy the PDFs or pictures to the RGN server; the page must keep loading them from GitHub so updates appear automatically.
- Report any check that fails to domagoj.vulin@rgn.unizg.hr, with the FAIL lines from Step 1.
