# Passport and visa photo requirements (JSON)

Load machine-readable photo requirements for 31 documents from 21 countries and regions. Each record includes dimensions, plain-English rules and dated government source links. Head-height ranges are explicitly marked as official requirements or practical ranges used by pixtidy.

## What's inside?

[`data/requirements.json`](data/requirements.json) contains the same data as [pixtidy's JSON dataset](https://pixtidy.com/requirements.json), updated **2026-10-05** and copied into this repository on **2026-10-07**. The [developer page](https://pixtidy.com/developers) provides the live, always-current version and documentation.

Coverage includes US passport, renewal, DS-160 visa, green card and baby passport requirements; UK visa and ETA; India OCI, e-Visa and passport; and Schengen visa requirements.

Other records cover China visa, Vietnam e-Visa and passport, Thailand e-Visa, Australia visa and passport, Brazil e-Visa, New Zealand passport and NZeTA, Nigeria passport, Pakistan visa, Tanzania e-Visa, Ethiopia e-Visa, Taiwan passport, Italy passport, Poland passport, UAE visa, Singapore visa, Turkey passport and Israel passport.

## Use it: how do you load and select requirements?

1. **Fetch the repository JSON and select a document in JavaScript.**

   ```javascript
   const r = await fetch(
     "https://raw.githubusercontent.com/agent-gigmole/pixtidy-visa-photo-specs/main/data/requirements.json"
   );
   const { documents } = await r.json();

   const schengen = documents.find(d => d.id === "schengen-visa");
   console.log(schengen.print_mm);
   // {width: 35, height: 45}
   ```

   The selected record gives a Schengen visa print size of **35 × 45 mm**. Use its other fields for the background, head-height range and rules.

2. **Load the local file and filter by country in Python.**

   ```python
   import json

   with open("data/requirements.json", encoding="utf-8") as f:
       dataset = json.load(f)

   country = input("Country value to match: ")
   matches = [
       d for d in dataset["documents"]
       if d["country"] == country
   ]

   for d in matches:
       print(d["id"], d["document"], d["print_mm"])
   ```

   This example prints the identifier, document name and print dimensions for each match.

3. **Inspect the selected record's references.**

   Continue the JavaScript example to display the linked government sources and their check dates:

   ```javascript
   for (const source of schengen.sources) {
     console.log(source.label, source.url, source.verified);
   }
   ```

   These references belong to the selected document.

## Fields: what does the JSON contain?

The top-level object describes the dataset and holds its document records.

| Field | Contents |
| --- | --- |
| `publisher` | Dataset publisher |
| `url` | Dataset URL |
| `updated` | Dataset update date |
| `license` | Data license |
| `documents[]` | Document requirement records |

Each entry in `documents[]` uses the following fields:

| Field | Contents |
| --- | --- |
| `id` | Document identifier |
| `country` | Country or region |
| `document` | Document name |
| `page` | pixtidy page for that document |
| `print_mm` | `{width, height}` in millimeters, or `null` |
| `pixels` | `{width, height}` in pixels, or `null` |
| `head_height` | `{min, max, unit, official}` |
| `background` | Background requirement |
| `file` | File or print-format notes, or `null` |
| `rules[]` | Plain-English rules |
| `sources[]` | Objects with `label`, `url` and a `verified` date |

`head_height.unit` is `"fraction_of_photo_height"`. Its `min` and `max` describe fractions of the full photo height.

`head_height.official` identifies the basis for the numeric range:

- **`true`** means the range comes from the official source.
- **`false`** means it is a practical range pixtidy uses where the authority publishes no numeric range.

The flag applies to the head-height range.

The nullable fields are `print_mm`, `pixels` and `file`. Their definitions remain separate: print dimensions use millimeters, digital dimensions use pixels, and format notes are text.

## Sources and accuracy: what should you verify?

Every document lists the official government sources from which its requirements were taken, with the date each source was checked; rules change, so always confirm against those linked official sources before submitting.

Watch for these three interpretation errors:

1. **Treating a practical range as an official numeric requirement.**  
   When `head_height.official` is `false`, label the range as practical.

2. **Confusing print dimensions with digital dimensions.**  
   The JavaScript example returns **35 × 45 mm**, not a pixel requirement. Read `pixels` separately, and allow for `null` in either dimension field.

3. **Using the wrong date or reference.**  
   `updated` describes the dataset, while each source's `verified` date records its check. The repository copy date is another event. Likewise, `page` links to pixtidy; the official government links are in `sources[]`.

## License: how should you attribute the data?

The data is licensed under **CC BY 4.0**. See [`LICENSE`](LICENSE) for the license.

Cite the official source associated with the reused document and **"pixtidy (https://pixtidy.com)"**.

## Corrections: how do you report an error?

[Open an issue](https://github.com/agent-gigmole/pixtidy-visa-photo-specs/issues) with a link to the official source.

Disclosure: Not affiliated with any government. Not legal advice.
