---
name: nutrition-label-extractor
description: Extract nutritional information, serving sizes, ingredients, and product details from food packaging images with high accuracy. Validates nutrition facts, normalizes units, detects OCR inconsistencies, and returns structured nutrition data while prioritizing accuracy over completeness.
---

# Nutrition Label Extractor

## Instructions

Analyze food packaging images and extract nutritional information with maximum accuracy.

### Primary Objective

Extract only information that is clearly visible in the image.

Accuracy is more important than completeness.

### Extraction Rules

- Never guess nutritional values.
- Never estimate missing information.
- Never infer values from similar products.
- Never calculate missing nutrients unless explicitly requested.
- If a value cannot be confidently read, return `null`.
- Preserve values exactly as shown on the label before normalization.
- Ignore marketing claims, slogans, and promotional text.
- Focus only on factual product information.

### Product Information

Extract when available:

- Product name
- Brand name
- Product description
- Net weight or package size
- Ingredients list

### Serving Information

Extract when available:

- Serving size
- Servings per package

### Nutrition Information

Extract the following nutrients when present:

- Energy (kJ)
- Calories (kcal)
- Protein
- Fat
- Saturated Fat
- Trans Fat
- Carbohydrates
- Sugars
- Fibre
- Sodium
- Salt

### Nutrition Table Detection

Nutrition labels may contain multiple columns.

Preferred extraction order:

1. Per 100g
2. Per 100ml
3. Per serving
4. Per package

Always identify which column each value belongs to.

### Field Normalization

Treat the following labels as equivalent:

#### Protein

- Protein
- Proteins

#### Carbohydrates

- Carbohydrate
- Carbohydrates
- Carbs

#### Sugars

- Sugar
- Sugars

#### Fibre

- Fibre
- Fiber
- Dietary Fibre

#### Fat

- Fat
- Total Fat

#### Saturated Fat

- Saturated Fat
- Saturates
- Saturated

#### Sodium

- Sodium

#### Salt

- Salt

### Unit Normalization

Normalize units to:

- Energy → kJ
- Calories → kcal
- Protein → g
- Fat → g
- Saturated Fat → g
- Trans Fat → g
- Carbohydrates → g
- Sugars → g
- Fibre → g
- Sodium → mg
- Salt → g

### Validation Rules

Validate extracted values before returning results.

#### Energy Validation

Verify whether calories are approximately consistent with macronutrients:

Calories ≈

- Protein × 4
- Carbohydrates × 4
- Fat × 9

If a significant discrepancy exists:

- Preserve the extracted value
- Add a warning

#### Serving Weight Validation

Check whether:

Protein + Carbohydrates + Fat

greatly exceeds the serving size.

If impossible or unlikely:

- Preserve extracted values
- Add a warning

#### Sodium and Salt Validation

When both sodium and salt are present, verify consistency.

Approximate relationship:

Salt ≈ Sodium × 2.5

If inconsistent:

- Add a warning

### OCR Quality Assessment

Reduce confidence when:

- Image is blurry
- Image is rotated
- Nutrition table is cropped
- Text is partially obscured
- Multiple values are possible
- Units are unclear
- Table structure is damaged

### Confidence Scoring

Return a confidence score between 0 and 100.

Guidelines:

- 95-100: Very clear label
- 80-94: Minor uncertainty
- 60-79: Moderate uncertainty
- Below 60: Significant uncertainty

### Ambiguity Handling

When multiple interpretations are possible:

- Choose the most likely value
- Record a warning explaining the ambiguity

If confidence is too low:

- Return `null`
- Add a warning

### Output Requirements

Return structured nutrition data.

For every extracted field include:

- value
- unit
- source column (per 100g, per 100ml, per serving, per package)

Include:

- confidence score
- warnings array

### Final Priority

Accuracy > Completeness

Returning `null` is always preferred over returning an incorrect nutritional value.
