After reading the scientific publication full text provided above, generate a formatted JSON file extracting the information below. Use "" only when a field is genuinely not determinable.

{
"records": [
    {
      "sensor_type": "(gas/bio/liquid; choose one)",
      "detect_target": "(target chemical name)",
      "lower_detection_limit": "xx <unit>",
      "upper_detection_limit": "xx <unit>",
      "probe_material": "(probe chemical name)",
      "test_operating_temperature (celcius)": "xx °C",
      "pH_value": "(use -1 for gas; otherwise a number, or a range xx-yy)",
      "test_medium": "(e.g. air, water, PBS)"
    }
   (add one record per distinct target or device the paper reports)
  ]
}

Rules:
1. Detection limits are the lowest and highest analyte amounts the device can detect (its sensing range). Always try to fill BOTH. For gas sensors use ppm/ppb/ppt. For liquid or ion sensors (including pH/H+ sensors) express the limits as molar concentration (M/mM/µM/nM/pM), not as a pH number.
2. test_operating_temperature: the temperature the sensing test is run at, in °C. If the paper does not state it, assume room temperature, i.e. 25 °C.
3. test_medium: the base medium the device is tested in (e.g. air, water, PBS). Report the medium only, without the analyte that is dissolved or mixed into it.
4. probe_material: the specific recognition or active sensing material, named as concisely as possible (the key functional species, not the whole material stack).
5. For detect_target and probe_material, use the common chemical name or formula.
