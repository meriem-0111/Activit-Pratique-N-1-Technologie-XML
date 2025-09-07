# Activit-Pratique-N-1-Technologie-XML
1. Structure graphique de l’arbre XML
L’arbre XML représente la hiérarchie des éléments du document :
releve (attribut : RIB)
│── dateReleve
│── solde
│── operations (attributs : dateDebut, dateFin)
    │── operation (attributs : type, date, montant, description)
    │── operation (attributs : type, date, montant, description)
    │── operation (attributs : type, date, montant, description)
    │── operation (attributs : type, date, montant, description)
2. Définition de la DTD et document XML valide
DTD (fichier releve.dtd)

<!ELEMENT releve (dateReleve, solde, operations)>
<!ATTLIST releve RIB CDATA #REQUIRED>

<!ELEMENT dateReleve (#PCDATA)>
<!ELEMENT solde (#PCDATA)>

<!ELEMENT operations (operation+)>
<!ATTLIST operations dateDebut CDATA #REQUIRED dateFin CDATA #REQUIRED>

<!ELEMENT operation EMPTY>
<!ATTLIST operation type (CREDIT|DEBIT) #REQUIRED
                      date CDATA #REQUIRED
                      montant CDATA #REQUIRED
                      description CDATA #REQUIRED>
XML valide avec la DTD
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE releve SYSTEM "releve.dtd">
<releve RIB="011112222333344445555666">
    <dateReleve>2021-11-10</dateReleve>
    <solde>14500</solde>
    <operations dateDebut="2021-01-01" dateFin="2021-01-30">
        <operation type="CREDIT" date="2021-01-01" montant="9000" description="Vers Espèce"/>
        <operation type="DEBIT" date="2021-01-11" montant="3400" description="Chèque Guichet"/>
        <operation type="DEBIT" date="2021-01-15" montant="120" description="Prélèvement Assurence"/>
        <operation type="CREDIT" date="2021-01-25" montant="70000" description="Virement"/>
    </operations>
</releve>
3. Définition du schéma XML et document valide
Schéma XML (fichier releve.xsd)

<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
    <xs:element name="releve">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="dateReleve" type="xs:date"/>
                <xs:element name="solde" type="xs:decimal"/>
                <xs:element name="operations">
                    <xs:complexType>
                        <xs:sequence>
                            <xs:element name="operation" maxOccurs="unbounded">
                                <xs:complexType>
                                    <xs:attribute name="type" type="xs:string" use="required"/>
                                    <xs:attribute name="date" type="xs:date" use="required"/>
                                    <xs:attribute name="montant" type="xs:decimal" use="required"/>
                                    <xs:attribute name="description" type="xs:string" use="required"/>
                                </xs:complexType>
                            </xs:element>
                        </xs:sequence>
                        <xs:attribute name="dateDebut" type="xs:date" use="required"/>
                        <xs:attribute name="dateFin" type="xs:date" use="required"/>
                    </xs:complexType>
                </xs:element>
            </xs:sequence>
            <xs:attribute name="RIB" type="xs:string" use="required"/>
        </xs:complexType>
    </xs:element>
</xs:schema>


4. Feuille de style XSL pour affichage HTML avec total débit/crédit
XSL (fichier releve_total.xsl)

<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="/">
        <html>
            <head>
                <title>Relevé Bancaire</title>
            </head>
            <body>
                <h2>Relevé Bancaire - RIB : <xsl:value-of select="/releve/@RIB"/></h2>
                <p>Date du relevé : <xsl:value-of select="/releve/dateReleve"/></p>
                <p>Solde : <xsl:value-of select="/releve/solde"/></p>
                <table border="1">
                    <tr>
                        <th>Type</th>
                        <th>Date</th>
                        <th>Montant</th>
                        <th>Description</th>
                    </tr>
                    <xsl:for-each select="/releve/operations/operation">
                        <tr>
                            <td><xsl:value-of select="@type"/></td>
                            <td><xsl:value-of select="@date"/></td>
                            <td><xsl:value-of select="@montant"/></td>
                            <td><xsl:value-of select="@description"/></td>
                        </tr>
                    </xsl:for-each>
                </table>

                <h3>Total Débit : 
                    <xsl:value-of select="sum(/releve/operations/operation[@type='DEBIT']/@montant)"/>
                </h3>
                <h3>Total Crédit : 
                    <xsl:value-of select="sum(/releve/operations/operation[@type='CREDIT']/@montant)"/>
                </h3>
            </body>
        </html>
    </xsl:template>
</xsl:stylesheet>


5. Feuille de style XSL pour affichage des opérations de type CREDIT
XSL (fichier releve_credit.xsl)

<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="/">
        <html>
            <head>
                <title>Opérations de Crédit</title>
            </head>
            <body>
                <h2>Opérations de Crédit - RIB : <xsl:value-of select="/releve/@RIB"/></h2>
                <table border="1">
                    <tr>
                        <th>Date</th>
                        <th>Montant</th>
                        <th>Description</th>
                    </tr>
                    <xsl:for-each select="/releve/operations/operation[@type='CREDIT']">
                        <tr>
                            <td><xsl:value-of select="@date"/></td>
                            <td><xsl:value-of select="@montant"/></td>
                            <td><xsl:value-of select="@description"/></td>
                        </tr>
                    </xsl:for-each>
                </table>
            </body>
        </html>
    </xsl:template>
</xsl:stylesheet>

