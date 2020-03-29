# UML

- generalization

  ![_images/uml_generalization.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_generalization.jpg)

  Generalization represent an is-a relationship. 

  While coding, generalization is represented by extending a non-abstract class.

- realization

  ![_images/uml_realize.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_realize.jpg)

  Realization also represents an is-a relationship.

  While coding, realization is represented by extending an abstract class.

- aggregation

  ![_images/uml_aggregation.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_aggregation.jpg)

  Aggregation represents a has relationship. The image above indicates **B** has **A**s.

  Aggregation is not a strong dependency, **A**s will not disappear if **B** is deleted.

- composition

  ![_images/uml_composition.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_composition.jpg)

  Composition also represent a has relationship.

  However, composition is a strong  dependency. **A**s will disappear if **B** is deleted.

- association

  ![_images/uml_association.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_association.jpg)

  Association is represented by a line. It usually does not have direction. Student and Student ID for example.

  However, direction in the image above indicates **A** recognize **B** but not the other way.

  While coding, association is represented by **member variable**.

- dependency

  ![_images/uml_dependency.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_dependency.jpg)

  Different from association, dependency can change during runtime. It's more like a temporary relationship.

  While coding, dependency can be represented by **parameters**.