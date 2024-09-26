# Web Google Maps

[![Demo](https://i.ytimg.com/vi/2UdG5ILDtiY/3.jpg)](https://youtu.be/5hvAubXgUnc "Demo")    

This module contains three new features:
- New view type and mode `"map"`
- New widget `"gplaces_address_autocomplete"`
- New widget `"gplaces_autocomplete"`
 
## Map view `"map"`
This new view `map` will integrate Google Maps into Odoo, enabling you to display `res.partner` geolocation on a map or any model containing geolocation. This feature works seamlessly with Odoo, allowing you to search for partner locations using Odoo's search functionality.

There are five available attributes that you can customize:
- `lat`: An attribute to specify the latitude field on the object __[mandatory]__
- `lng`: An attribute to specify the longitude field on the object __[mandatory]__
- `color`: An attribute to modify marker color (optional); any given color will set all markers to that color __[optional]__
- `colors`: Works like the `color` attribute but is more configurable (you can set marker colors depending on their value). This attribute works similarly to `colors` in the tree view on Odoo 9.0 __[optional]__
- `library`: An attribute to specify which map library to load __[mandatory]__.   
    This option has two values:   
    1. `geometry`
    2. `drawing`

### How to create the view?    
Example:
```xml
<!-- View -->
<record id="view_res_partner_map" model="ir.ui.view">
    <field name="name">view.res.partner.map</field>
    <field name="model">res.partner</field>
    <field name="arch" type="xml">
        <map class="o_res_partner_map" library='geometry' string="Map" lat="partner_latitude" lng="partner_longitude" colors="blue:company_type=='person';green:company_type=='company';">
            <field name="id"/>
            <field name="partner_latitude"/>
            <field name="partner_longitude"/>
            <field name="company_type"/>
            <field name="color"/>
            <field name="display_name"/>
            <field name="title"/>
            <field name="email"/>
            <field name="parent_id"/>
            <field name="is_company"/>
            <field name="function"/>
            <field name="phone"/>
            <field name="street"/>
            <field name="street2"/>
            <field name="zip"/>
            <field name="city"/>
            <field name="country_id"/>
            <field name="mobile"/>
            <field name="state_id"/>
            <field name="category_id"/>
            <field name="image_small"/>
            <field name="type"/>
            <templates>
                <t t-name="kanban-box">
                    <div class="oe_kanban_global_click o_res_partner_kanban">
                        <div class="o_kanban_image">
                            <t t-if="record.image_small.raw_value">
                                <img t-att-src="kanban_image('res.partner', 'image_small', record.id.raw_value)"/>
                            </t>
                            <t t-if="!record.image_small.raw_value">
                                <t t-if="record.type.raw_value === 'delivery'">
                                    <img t-att-src='_s + "/base/static/src/img/truck.png"' class="o_kanban_image oe_kanban_avatar_smallbox"/>
                                </t>
                                <t t-if="record.type.raw_value === 'invoice'">
                                    <img t-att-src='_s + "/base/static/src/img/money.png"' class="o_kanban_image oe_kanban_avatar_smallbox"/>
                                </t>
                                <t t-if="record.type.raw_value != 'invoice' &amp;&amp; record.type.raw_value != 'delivery'">
                                    <t t-if="record.is_company.raw_value === true">
                                        <img t-att-src='_s + "/base/static/src/img/company_image.png"'/>
                                    </t>
                                    <t t-if="record.is_company.raw_value === false">
                                        <img t-att-src='_s + "/base/static/src/img/avatar.png"'/>
                                    </t>
                                </t>
                            </t>
                        </div>
                        <div class="oe_kanban_details">
                            <strong class="o_kanban_record_title oe_partner_heading">
                                <field name="display_name"/>
                            </strong>
                            <div class="o_kanban_tags_section oe_kanban_partner_categories">
                                <span class="oe_kanban_list_many2many">
                                    <field name="category_id" widget="many2many_tags" options="{'color_field': 'color'}"/>
                                </span>
                            </div>
                            <ul>
                                <li t-if="record.parent_id.raw_value and !record.function.raw_value">
                                    <field name="parent_id"/>
                                </li>
                                <li t-if="!record.parent_id.raw_value and record.function.raw_value">
                                    <field name="function"/>
                                </li>
                                <li t-if="record.parent_id.raw_value and record.function.raw_value">
                                    <field name="function"/> at <field name="parent_id"/>
                                </li>
                                <li t-if="record.city.raw_value and !record.country_id.raw_value">
                                    <field name="city"/>
                                </li>
                                <li t-if="!record.city.raw_value and record.country_id.raw_value">
                                    <field name="country_id"/>
                                </li>
                                <li t-if="record.city.raw_value and record.country_id.raw_value">
                                    <field name="city"/>
                                    , <field name="country_id"/>
                                </li>
                                <li t-if="record.email.raw_value" class="o_text_overflow">
                                    <field name="email"/>
                                </li>
                            </ul>
                            <div class="oe_kanban_partner_links"/>
                        </div>
                    </div>
                </t>
            </templates>
        </map>
    </field>
</record>
    
<!-- Action -->
<record id="action_partner_map" model="ir.actions.act_window">
    ...
    <field name="view_type">form</field>
    <field name="view_mode">tree,form,map</field>
    ...
</record>
The view looks familiar?
Yes, you're right.
The marker infowindow will use kanban-box kanban card style.
How to set up color for markers on the map?

There are two attributes:

    colors: Allows you to display different marker colors to represent records on the map
    color: Sets one marker color for all records on the map

Example:

xml

<!-- colors -->
<map string="Map" lat="partner_latitude" lng="partner_longitude" colors="green:company_type=='person';blue:company_type=='company';">
    ...
</map>

<!-- color -->
<map string="Map" lat="partner_latitude" lng="partner_longitude" color="orange">
    ...
</map>

New widget "gplaces_address_autocomplete"

This new widget integrates Place Autocomplete Address Form into Odoo.
The widget has four options that can be modified:

    component_form
    fillfields
    lat
    lng

Component form component_form

This option modifies which values to take from objects returned by the geocoder.
Full documentation about Google component types can be found here.
By default, this option is configured as follows:

javascript

{
    'street_number': 'long_name',
    'route': 'long_name',
    'intersection': 'short_name',
    'political': 'short_name',
    'country': 'short_name',
    'administrative_area_level_1': 'short_name',
    'administrative_area_level_2': 'short_name',
    'administrative_area_level_3': 'short_name',
    'administrative_area_level_4': 'short_name',
    'administrative_area_level_5': 'short_name',
    'colloquial_area': 'short_name',
    'locality': 'short_name',
    'ward': 'short_name',
    'sublocality_level_1': 'short_name',
    'sublocality_level_2': 'short_name',
    'sublocality_level_3': 'short_name',
    'sublocality_level_5': 'short_name',
    'neighborhood': 'short_name',
    'premise': 'short_name',
    'postal_code': 'short_name',
    'natural_feature': 'short_name',
    'airport': 'short_name',
    'park': 'short_name',
    'point_of_interest': 'long_name'
}

This configuration can be modified in the view field definition.
Example:

xml

<record id="view_res_partner_form" model="ir.ui.view">
   ...
   <field name="arch" type="xml">
        ...
        <field name="street" widget="gplaces_address_form" options="{'component_form': {'street_number': 'short_name'}}"/>
        ...
    </field>
</record>

Fill fields fillfields

This option influences the gplaces_address_autocomplete widget.
This option should contain known fields that the widget will automatically fill.
A field can contain one or multiple elements of the component form.
By default, this option is configured as follows:

javascript

{
    'street': ['street_number', 'route'],
    'street2': ['administrative_area_level_3', 'administrative_area_level_4', 'administrative_area_level_5'],
    'city': ['locality', 'administrative_area_level_2'],
    'zip': 'postal_code',
    'state_id': 'administrative_area_level_1',
    'country_id': 'country',
}

This configuration can be modified in the view field definition as well.
Example:

xml

<record id="view_res_partner_form" model="ir.ui.view">
    ...
    <field name="arch" type="xml">
        ...
        <field name="street" widget="google_places" options="{'fillfields': {'street2': ['route', 'street_number']}}"/>
        ...
    </field>
</record>

Latitude lat and Longitude lng

These options specify the fields for geolocation, allowing the widget to fill them automatically.
New widget "gplaces_autocomplete"

This new widget integrates Place Autocomplete into Odoo.
This widget has a similar configuration to gplaces_address_autocomplete.
Component form component_form

Same configuration as gplaces_address_autocomplete component form.
Fill fields fillfields

This configuration works similarly to gplaces_address_autocomplete.
By default, this option is configured as follows:

javascript

{
    general: {
        name: 'name',
        website: 'website',
        phone: ['international_phone_number', 'formatted_phone_number']
    },
    geolocation: {
        partner_latitude: 'latitude',
        partner_longitude: 'longitude'
    },
    address: {
        street: ['street_number', 'route'],
        street2: ['administrative_area_level_3', 'administrative_area_level_4', 'administrative_area_level_5'],
        city: ['locality', 'administrative_area_level_2'],
        zip: 'postal_code',
        state_id: 'administrative_area_level_1',
        country_id: 'country'
    }
};

Technical

This module will install base_setup and base_geolocalize.
We recommend setting up the Google Maps API Key and adding it into Odoo's Settings > General Settings after installing this module.
List of Google APIs & Services Required:

    Geocoding API
    Maps JavaScript API
    Places API

Visit this page to learn how to get a Google API Key.