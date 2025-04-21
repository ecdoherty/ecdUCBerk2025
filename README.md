Readme
This model is a valuable internal tool to support data-informed pricing, inventory prioritization, and operational trade-offs in used car dealerships.
* Key Drivers Identified:
    * Vehicle age & condition have the largest price impact
    * Manufacturer, fuel type, and cylinders influence pricing patterns significantly
    * There are other, Non-linear patterns that should be considered to best fine-tune inventory (e.g., diminishing returns after a certain age or mileage)

The model's structure allows for actionable insights based on feature types:
* Ordinal Features (e.g., condition, cylinders, car_age)
    * Can show how price increases across these features and enable more informed business decisions. For example, dealers can prioritize cosmetic repairs to upgrade inventory from "good" to "excellent," which often commands a price premium.
* Nominal Features (e.g., manufacturer, fuel, type)
    * Model can help with comparisons in each category, helping identify manufacturers or certain features that consistently outperform others.
* Numerical Features (e.g., year, odometer)
    * This model illustrates how incremental changes in these features can affect price. For example, price drops consistently as cars age or accrue mileage. This is some what obvious, but the model confirms this as well as helps dealers identify at what threshold to make pricing optimizations such as prioritizing certain low-mileage vehicles for premium listings, or bundle warranties for certain high-mileage vehicles to justify pricing.
