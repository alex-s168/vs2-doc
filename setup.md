# Setup
```groovy
repositories {
  mavenCentral()
  maven { url "https://maven.valkyrienskies.org" }
}
```

`gradle.properties`:
```kt
vs2_mc_version=118
vs2_version=2.1.1-beta.1+9445389ac1
vs_core_version=1.1.0+8a93383ce5
```

some valid combinations of the above values:
| `vs2_mc_version` | `vs2_version` | `vs_core_version` | notes |
| - | - | - | - |
| `118` | `2.1.1-beta.1+9445389ac1` | `1.1.0+8a93383ce5` | |
| `118` | `2.1.1-beta.6+eac56407b3` | `1.1.0+04c69b50d2` | latest VS2 version, but **no addons currently support it** |
| `120` | `2.1.3-beta.1+a52f38bd68` | `1.1.0+8a93383ce5` | |
| `120` | `2.3.0-beta.6+a9ed0529b2` | `1.1.0+da86725858` | latest VS2 version, but **no addons currently support it** |

Continue here:
- [Gradle Loom](#gradle-loom)
- [Forge Gradle](#forge-gradle)

# Gradle Loom
common:
```groovy
dependencies {
  implementation "org.joml:joml:1.10.4" { transitive = false }
  implementation "org.joml:joml-primitives:1.10.0" { transitive = false }
  
  modApi "org.valkyrienskies:valkyrienskies-${rootProject.vs2_mc_version}-common:${rootProject.vs2_version}"
  
  compileOnly "org.valkyrienskies.core:api:${rootProject.vs_core_version}"
  compileOnly "org.valkyrienskies.core:api-game:${rootProject.vs_core_version}"
  compileOnly "org.valkyrienskies.core:util:${rootProject.vs_core_version}"
  compileOnly "org.valkyrienskies.core:impl:${rootProject.vs_core_version}"
}
```

fabric:
```groovy
dependencies {
  modApi "org.valkyrienskies:valkyrienskies-${rootProject.vs2_mc_version}-fabric:${rootProject.vs2_version}"
}
```

forge:
```groovy
dependencies {
  modApi "org.valkyrienskies:valkyrienskies-${rootProject.vs2_mc_version}-forge:${rootProject.vs2_version}" { transitive = false }
}
```

# Forge Gradle
common:
```groovy
dependencies {
  implementation "org.joml:joml:1.10.4" { transitive = false }
  implementation "org.joml:joml-primitives:1.10.0" { transitive = false }
  
  implementation fg.deobf("org.valkyrienskies:valkyrienskies-${rootProject.vs2_mc_version}-common:${rootProject.vs2_version}")
  
  compileOnly "org.valkyrienskies.core:api:${rootProject.vs_core_version}"
  compileOnly "org.valkyrienskies.core:api-game:${rootProject.vs_core_version}"
  compileOnly "org.valkyrienskies.core:util:${rootProject.vs_core_version}"
  compileOnly "org.valkyrienskies.core:impl:${rootProject.vs_core_version}"
}
```

fabric:
```groovy
dependencies {
  implementation fg.deobf("org.valkyrienskies:valkyrienskies-${rootProject.vs2_mc_version}-fabric:${rootProject.vs2_version}")
}
```

forge:
```groovy
dependencies {
  implementation fg.deobf("org.valkyrienskies:valkyrienskies-${rootProject.vs2_mc_version}-forge:${rootProject.vs2_version}") { transitive = false }
}
```
