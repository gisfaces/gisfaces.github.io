![](https://www.gisfaces.com/wp-content/uploads/gis-faces-logo.png)

## Welcome to GISFaces
[GISFaces](https://www.gisfaces.com)™ is an Enterprise GIS mapping component library for [JavaServer Faces (JSF)](http://www.javaserverfaces.org). The underlying GIS mapping engine used is the [ESRI](https://www.esri.com)® [ArcGIS API For JavaScript](https://developers.arcgis.com/javascript/). Any ArcGIS layer or raw data, local or online, can be used in the maps you build. Best of all, no prior GIS programmer or analyst experience is necessary to start integrating GIS maps into your enterprise applications.

## Features
- Custom components include a map, map symbol image, SVG graphic, and geo-location tracking.
- 2D and 3D map views available.
- Map layer types supported include Bing, CSV, Feature, GeoJSON, GeoRSS, Graphics, Imagery, Integrated Mesh, KML, Map Image, Point Cloud, ArcGIS Portal, Scene, Stream, Tile, Vector Tile, WMS, and WMTS.
- All layers automatically display the feature attribute table on selection. This popup template is fully customizable from the Java object model.
- Java event handlers for map interaction include basemap selection, map click, map extent, feature selection, geo-location, graphic editing.
- The map widgets displayed are search, zoom in/out, pan/orientation, compass, basemap, layer list, legend, geo-location tracking, full screen, 2d/3d toggle, and coordinate transformations.
- Layers are built in Java code using an extensive object model.

## Examples
All the [latest](http://geergis.com:8080/gisfaces-examples-snapshot/) and [legacy](http://www.geergis.com:8080/gisfaces-examples/) example maps are available live.

## Usage
#### Aeronautical Map Example

#### XHTML
~~~xhtml
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://java.sun.com/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui"
	xmlns:pe="http://primefaces.org/ui/extensions"
	xmlns:gis="http://gisfaces.com">

<ui:composition template="/view/template/template.xhtml">

	<ui:define name="title">
		<h:outputText value="GISFaces - Aeronautical Map"></h:outputText>
	</ui:define>

	<ui:define name="content">

		<p:growl id="growl1" showSummary="true" showDetail="true" keepAlive="true"></p:growl>

		<pe:layoutPane position="west" size="300" closable="true" resizable="false">
			<f:facet name="header">
				<h:outputText value="Layers"></h:outputText>
			</f:facet>
			<h:form id="westForm">
				<p:panel header="Aeronautical" styleClass="containerPanel" toggleable="true">
					<h:panelGrid columns="2">
						<p:toggleSwitch value="#{aeronauticalView.airports.visible}">
							<f:ajax render=":mapForm:map1"></f:ajax>
						</p:toggleSwitch>
						<h:outputText styleClass="bold" value="Airports"></h:outputText>
					</h:panelGrid>
					<h:panelGrid columns="2">
						<p:toggleSwitch value="#{aeronauticalView.runways.visible}">
							<f:ajax render=":mapForm:map1"></f:ajax>
						</p:toggleSwitch>
						<h:outputText styleClass="bold" value="Runways"></h:outputText>
					</h:panelGrid>
				</p:panel>
				<p:panel header="Weather" styleClass="containerPanel" toggleable="true">
					<h:panelGrid columns="2">
						<p:toggleSwitch  value="#{aeronauticalView.warnings.visible}">
							<f:ajax render=":mapForm:map1 warningsSlider"></f:ajax>
						</p:toggleSwitch >
						<h:outputText styleClass="bold" value="Weather Warnings"></h:outputText>
					</h:panelGrid>
					<h:panelGrid width="100%" title="Opacity">
						<p:slider id="warningsSlider" for="warningsOpacity" minValue="0.0" maxValue="1.0" step="0.01" disabled="#{not aeronauticalView.warnings.visible}">
							<p:ajax event="slideEnd" process="warningsOpacity" update=":mapForm:map1"></p:ajax>
						</p:slider>
						<h:inputHidden id="warningsOpacity" value="#{aeronauticalView.warnings.opacity}"></h:inputHidden>
					</h:panelGrid>
					<h:panelGrid columns="2">
						<p:toggleSwitch value="#{aeronauticalView.radar.visible}">
							<f:ajax render=":mapForm:map1 radarSlider"></f:ajax>
						</p:toggleSwitch >
						<h:outputText styleClass="bold" value="Doppler Radar"></h:outputText>
					</h:panelGrid>
					<h:panelGrid width="100%" title="Opacity">
						<p:slider id="radarSlider" for="radarOpacity" minValue="0.0" maxValue="1.0" step="0.01" disabled="#{not aeronauticalView.radar.visible}">
							<p:ajax event="slideEnd" process="radarOpacity" update=":mapForm:map1"></p:ajax>
						</p:slider>
						<h:inputHidden id="radarOpacity" value="#{aeronauticalView.radar.opacity}"></h:inputHidden>
					</h:panelGrid>
				</p:panel>
			</h:form>
		</pe:layoutPane>

		<pe:layoutPane position="center">
			<f:facet name="header">
				<h:outputText value="Aeronautical Map"></h:outputText>
			</f:facet>
			<h:form id="centerForm" style="height:100%;">
				<h:panelGroup id="panel1" layout="block" styleClass="gisfaces-map-panel"></h:panelGroup>
			</h:form>
		</pe:layoutPane>

		<h:form id="mapForm">
			<gis:map id="map1" panel=":centerForm:panel1" basemap="#{aeronauticalView.basemap}" latitude="#{aeronauticalView.latitude}" longitude="#{aeronauticalView.longitude}" zoom="#{aeronauticalView.zoom}" model="#{aeronauticalView.model}">
				<f:ajax event="basemap" listener="#{aeronauticalView.doMapBasemapListener}" render="growl1"></f:ajax>
				<f:ajax event="click" listener="#{aeronauticalView.doMapClickListener}" render="growl1"></f:ajax>
				<f:ajax event="extent" listener="#{aeronauticalView.doMapExtentListener}" render="growl1"></f:ajax>
				<f:ajax event="select" listener="#{aeronauticalView.doMapSelectListener}" render="growl1"></f:ajax>
			</gis:map>
		</h:form>

	</ui:define>

</ui:composition>

</html>
~~~

#### Java
~~~java
package com.gisfaces.examples.transportation;

import java.io.Serializable;

import javax.annotation.PostConstruct;
import javax.enterprise.context.SessionScoped;
import javax.inject.Named;

import com.gisfaces.model.layer.MapImageLayer;

@Named
@SessionScoped
public class AeronauticalView extends WeatherView implements Serializable
{
	private MapImageLayer airports;
	private MapImageLayer runways;

	@PostConstruct
	public void init()
	{
		super.init();

		// Build a map layer.
		airports = new MapImageLayer("airports", "https://maps.bts.dot.gov/services/rest/services/NTAD/Airports/MapServer");

		// Build a map layer.
		runways = new MapImageLayer("runways", "https://maps.bts.dot.gov/services/rest/services/NTAD/Runways/MapServer");

		// Some services may require the included proxy to be enabled.
		this.getModel().getConfiguration().setProxyEnabled(true);

		// Initialize the map view.
		this.getModel().getLayers().add(airports);
		this.getModel().getLayers().add(runways);
	}

	public MapImageLayer getAirports()
	{
		return airports;
	}

	public void setAirports(MapImageLayer airports)
	{
		this.airports = airports;
	}

	public MapImageLayer getRunways()
	{
		return runways;
	}

	public void setRunways(MapImageLayer runways)
	{
		this.runways = runways;
	}
}
~~~
