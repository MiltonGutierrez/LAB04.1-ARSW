# Escuela Colombiana de Ingeniería
# Arquitecturas de Software - ARSW
### Taller – Principio de Inversión de dependencias, Contenedores Livianos e Inyección de dependencias.

Parte I. Ejercicio básico.

Para ilustrar el uso del framework Spring, y el ambiente de desarrollo para el uso del mismo a través de Maven (y NetBeans), se hará la configuración de una aplicación de análisis de textos, que hace uso de un verificador gramatical que requiere de un corrector ortográfico. A dicho verificador gramatical se le inyectará, en tiempo de ejecución, el corrector ortográfico que se requiera (por ahora, hay dos disponibles: inglés y español).

1. Abra el los fuentes del proyecto en NetBeans.


2. Revise el archivo de configuración de Spring ya incluido en el proyecto (src/main/resources). El mismo indica que Spring buscará automáticamente los 'Beans' disponibles en el paquete indicado.

	Podemos observar el código XML de la aplicationContext que indica esto: 
	
	```xml

		<?xml version="1.0" encoding="UTF-8"?>
		<beans xmlns="http://www.springframework.org/schema/beans"
	   			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       			xmlns:context="http://www.springframework.org/schema/context"
	
	       			xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	          		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd">
   			<context:component-scan base-package="edu.eci.arsw" />
		</beans>
	```

3. Haciendo uso de la [configuración de Spring basada en anotaciones](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-spring-beans-and-dependency-injection.html) marque con las anotaciones @Autowired y @Service las dependencias que deben inyectarse, y los 'beans' candidatos a ser inyectadas -respectivamente-:

	* GrammarChecker será un bean, que tiene como dependencia algo de tipo 'SpellChecker'.
		Teniendo en cuenta que esta clase nos provee con la funcionalidad de verificar la gramática (aun no esta implementado) la anotación indicada será
		@Service. Para poder indicar que la clase tiene una dependencia que requerimos que se inyecte utilizamos la anotación @AutoWired. Finalmente 
		para poder identificar que diccionario usar utilizamos la anotacion @Qualifier para poder diferenciarlo como se muestra en el siguiente código

		```java
			@Service //Este es el bean que identifique como servicio 
			public class GrammarChecker {

			@Autowired
			@Qualifier("SpanishChecker")
			SpellChecker sc;
		```

	* EnglishSpellChecker y SpanishSpellChecker son los dos posibles candidatos a ser inyectados. Se debe seleccionar uno, u otro, mas NO ambos (habría conflicto de resolución de dependencias). Por ahora haga que se use EnglishSpellChecker.
		Identifique estos dos clases como simples componentes con la anotación @Component además de utilizar @Qualifier para que Spring pueda identificarlos al momento de hacer la inyección de dependencias, como se muestra en el siguiente código: 

		- *EnglishSpellChecker:*
			```java
				@Component
				@Qualifier("EnglishChecker")
				public class EnglishSpellChecker implements SpellChecker {

					@Override
					public String checkSpell(String text) {		
						return "Checked with english checker:"+text;
					}	
				}
			```

		- *SpanishSpellChecker:*
		```java
			@Component
			@Qualifier("SpanishChecker")
			public class SpanishSpellChecker implements SpellChecker {

				@Override
				public String checkSpell(String text) {
					return "revisando ("+text+") con el verificador de sintaxis del espanol";           
				}
			}
		```
5.	Haga un programa de prueba, donde se cree una instancia de GrammarChecker mediante Spring, y se haga uso de la misma:

	```java
	public static void main(String[] args) {
		ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
		GrammarChecker gc=ac.getBean(GrammarChecker.class);
		System.out.println(gc.check("la la la "));
	}
	```
	
6.	Modifique la configuración con anotaciones para que el Bean ‘GrammarChecker‘ ahora haga uso del  la clase SpanishSpellChecker (para que a GrammarChecker se le inyecte EnglishSpellChecker en lugar de  SpanishSpellChecker. Verifique el nuevo resultado.
