# Passo 1: Instalar as bibliotecas necessárias
# A biblioteca Trimesh é ótima para trabalhar com malhas 3D
!pip install trimesh pyrender

# Passo 2: Importar as bibliotecas
import trimesh
import numpy as np

# Passo 3: Criar a forma 3D do "cabeça de pedra"
# Para simplificar, vamos criar uma forma básica.
# Para algo mais realista, você precisaria de um arquivo de malha 3D (.stl, .obj).
# Aqui, vamos criar uma forma que combina um cubo e uma esfera, simulando uma cabeça de pedra.

# Crie uma esfera para a parte superior
esfera = trimesh.creation.icosphere(subdivisions=2, radius=10)

# Crie um cubo para a parte inferior (mandíbula/pescoço)
cubo = trimesh.creation.box(extents=[15, 15, 15])

# Junte as duas formas. Para esta simulação, vamos apenas colocar uma em cima da outra.
# Para uma união "booleana" mais complexa, você precisaria de uma biblioteca como `pygmsh` ou `blender`.
# Vamos apenas transladar o cubo para que ele fique abaixo da esfera.
cubo.apply_translation([0, 0, -5])

# Junte as duas malhas em uma só
cabeca_bruta = trimesh.util.concatenate([esfera, cubo])

# Passo 4: Centralizar o objeto
# A origem da impressora 3D (plano XY) é o ponto (0, 0, 0).
# Para centralizar o objeto, calculamos o centro de massa e o movemos para a origem.
# O `transform_to_origin()` faz isso automaticamente.
cabeca_bruta.apply_translation(-cabeca_bruta.center_mass)

# Passo 5: Ajustar o tamanho do objeto
# Vamos definir um tamanho razoável em milímetros (mm).
# Uma altura de 50mm (5cm) é um bom tamanho de exemplo.
altura_desejada = 50.0  # em mm

# Encontre a altura atual do objeto (extensão Z)
tamanho_atual_z = cabeca_bruta.extents[2]

# Calcule o fator de escala
fator_escala = altura_desejada / tamanho_atual_z

# Aplique o fator de escala a todo o objeto
cabeca_final = cabeca_bruta.copy()
cabeca_final.apply_scale(fator_escala)

# Passo 6: Verificar se o objeto está centralizado e no tamanho correto
# Para garantir que ele esteja no meio da planície, a coordenada Z mínima deve ser 0.
# A função `center_on_plane` centraliza e move o objeto para que a base esteja no plano XY.
cabeca_final.center_on_plane(plane_normal=[0, 0, 1], plane_origin=[0, 0, 0])

print(f"Objeto 'Cabeça de Pedra' criado e ajustado.")
print(f"Tamanho final do objeto (L x A x P): {np.round(cabeca_final.extents, 2)} mm")

# Passo 7: Salvar o objeto em um arquivo STL
# Este arquivo será gerado no ambiente do Google Colab e você pode baixá-lo.
nome_do_arquivo = 'cabeca_de_pedra.stl'
cabeca_final.export(nome_do_arquivo)

print(f"Objeto salvo como '{nome_do_arquivo}'")

# Opcional: Visualizar o objeto (pode ser lento no Colab)
# O `pyrender` pode ser usado para visualização, mas é um pouco complexo de configurar no Colab
# sem uma tela virtual. A maneira mais fácil é baixar o arquivo .stl e visualizá-lo em um
# programa como o Windows 3D Viewer ou MeshLab.
