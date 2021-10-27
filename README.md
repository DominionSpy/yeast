# Yeast - The text file leavening agent
Bake your flat text files with Yeast and end up with yummy XML bread.

## What is Yeast?
Yeast takes a structured text file and an XML Schema Definition file and produces an XML representation of the data. This is useful for the plethora of tools out there that process and transform XML.

## How do I create the XSD file?
For the most part Yeast uses a simplified XSD format, but it provides some additional attributes to define certain properties of the text file, such as separators, fixed widths, prefixes and suffixes.

The XSD must only have one root element and certain structures are not allowed, such as the "all" group type. Groups of data must be represented by a complex type and the data itself by a simple type.

As an example, here is a structured text file:
```
HEAD;ME0075632;MATLIST;3;
ORDR;OR7143;CU1157;
CONS;BT157-422;BIRD TABLE;2021-11-13;
MATL;MA453;WOOD;3;
MATL;MA327;FELT;1;
FOOT;6;ME0075632;
```

The XSD for this file is:
```XML
<?xml version="1.0" encoding="windows-1252" ?>
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
            xmlns:y="http://dev.dominionspy.com/xmlns/yeast"
            xmlns="http://dev.dominionspy.com/xmlns/material-list"
            targetNamespace="http://dev.dominionspy.com/xmlns/material-list"
            version="3">
  <xsd:element name="message" y:separator=";">
    <xsd:complexType>
      <xsd:sequence>
        <xsd:element name="message-header" type="message-header-type" y:separator=";" />
        <xsd:element name="order" type="order-type" y:separator=";" />
        <xsd:element name="message-footer" type="message-footer-type" y:separator=";" />
      </xsd:sequence>
    </xsd:complexType>
  </xsd:element>
  <xsd:complexType name="message-header-type">
    <xsd:sequence>
      <xsd:element name="segment-id" type="segment-id-type" fixed="HEAD" />
      <xsd:element name="message-id" type="message-id-type" />
      <xsd:element name="message-type" type="message-type-type" />
      <xsd:element name="message-version-number" type="xsd:integer" />
    </xsd:sequence>
  </xsd:complexType>
  <xsd:complexType name="order-type">
    <xsd:sequence>
      <xsd:element name="segment-id" type="segment-id-type" fixed="ORDR" />
      <xsd:element name="order-id" type="order-id-type" />
      <xsd:element name="customer-id" type="customer-id-type" />
      <xsd:element name="construction" type="construction-type" maxOccurs="unbounded" separator=";" />
      <xsd:element name="material-list" type="material-list-type" maxOccurs="unbounded" separator=";" />
    </xsd:sequence>
  </xsd:complexType>
  <xsd:complexType name="message-footer-type">
    <xsd:sequence>
      <xsd:element name="segment-id" type="segment-id-type" fixed="FOOT" />
      <xsd:element name="number-of-segments" type="xsd:integer" />
      <xsd:element name="message-id" type="message-id-type" />
    </xsd:sequence>
  </xsd:complexType>
  <xsd:complexType name="construction-type">
    <xsd:sequence>
      <xsd:element name="segment-id" type="segment-id-type" fixed="CONS" />
      <xsd:element name="construction-id" type="construction-id-type" />
      <xsd:element name="construction-description" type="xsd:string" />
      <xsd:element name="construction-date" type="date-type" />
      <xsd:element name="material" type="material-type" maxOccurs="unbounded" separator=";" />
    </xsd:sequence>
  </xsd:complexType>
  <xsd:complexType name="material-type">
    <xsd:sequence>
      <xsd:element name="segment-id" type="segment-id-type" fixed="MATL" />
      <xsd:element name="material-id" type="material-id-type" />
      <xsd:element name="material-description" type="xsd:string" />
      <xsd:element name="material-quantity" type="xsd:integer" />
    </xsd:sequence>
  </xsd:complexType>
  <xsd:simpleType name="segment-id-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="4" />
      <xsd:pattern value="[A-Z]{4}" />
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="message-id-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="9" />
      <xsd:pattern value="ME[0-9]{7}" />
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="message-type-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="7" />
      <xsd:enumeration value="MATLIST" />
      <xsd:enumeration value="RECEIPT" />
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="order-id-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="6" />
      <xsd:pattern value="OR[0-9]{4}" />
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="customer-id-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="6" />
      <xsd:pattern value="CU[0-9]{4}" />
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="construction-id-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="9" />
      <xsd:pattern value="[A-Z]{2}[0-9]{3}-[0-9]{3}" />
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="date-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="10" />
      <xsd:pattern value="[0-9]{4}-[0-9]{2}-[0-9]{2}" />
    </xsd:restriction>
  </xsd:simpleType>
  <xsd:simpleType name="material-id-type">
    <xsd:restriction base="xsd:string">
      <xsd:length value="5" />
      <xsd:pattern value="MA[0-9]{3}" />
    </xsd:restriction>
  </xsd:simpleType>
</xsd:schema>
```

## Can Yeast produce other well-known formats, such as JSON?
The initial release of Yeast will focus on producing XML only.
