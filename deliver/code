import streamlit as st
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import plotly.graph_objects as go
import plotly.express as px
from plotly.subplots import make_subplots
import networkx as nx
import time
import random
import math
from datetime import datetime

# 페이지 설정
st.set_page_config(
    page_title="영양분 전달 네트워크 효율성 시뮬레이션",
    page_icon="🌱",
    layout="wide",
    initial_sidebar_state="expanded"
)

# 타이틀과 설명
st.title("🌱 영양분 전달 네트워크 효율성 시뮬레이션")
st.markdown("""
**생물학적 시스템의 영양분 전달 방식을 모델링하고 효율성을 비교분석합니다**

각기 다른 생물학적 네트워크(식물의 관다발계, 동물의 신경계/순환계, 균류의 균사체)의 
영양분 전달 메커니즘을 시뮬레이션하여 효율성을 비교합니다.
""")

# 사이드바 설정
st.sidebar.header("🔬 시뮬레이션 설정")

# 기본 설정
st.sidebar.subheader("기본 매개변수")
nutrient_concentration = st.sidebar.slider("영양분 농도 (mg/L)", 1, 100, 50, 1)
network_nodes = st.sidebar.slider("네트워크 노드 수", 5, 50, 20, 1)
transport_speed = st.sidebar.slider("전달 속도 (cm/s)", 0.1, 10.0, 2.0, 0.1)

# 생물학적 매개변수
st.sidebar.subheader("생물학적 매개변수")
metabolic_efficiency = st.sidebar.slider("대사 효율성 (%)", 10, 100, 70, 1)
diffusion_coeff = st.sidebar.slider("확산 계수", 0.1, 5.0, 1.5, 0.1)
energy_consumption = st.sidebar.slider("에너지 소비율", 0.1, 3.0, 1.0, 0.1)

# 환경 조건
st.sidebar.subheader("환경 조건")
temperature = st.sidebar.slider("온도 (°C)", 5, 40, 25, 1)
humidity = st.sidebar.slider("습도 (%)", 20, 100, 60, 1)
ph_level = st.sidebar.slider("pH 수준", 4.0, 9.0, 7.0, 0.1)

# 네트워크 유형 선택
network_type = st.sidebar.selectbox(
    "분석할 네트워크",
    ["모든 네트워크 비교", "관다발계 (식물)", "신경계 (동물)", "순환계 (동물)", "균사체 (균류)"],
    index=0
)

# 고급 설정
with st.sidebar.expander("고급 설정"):
    simulation_time = st.slider("시뮬레이션 시간 (초)", 10, 300, 60)
    noise_level = st.slider("환경 노이즈 수준", 0.0, 0.5, 0.1, 0.05)
    network_damage = st.slider("네트워크 손상률 (%)", 0, 50, 0, 5)

# 시뮬레이션 실행 버튼
run_simulation = st.sidebar.button("🚀 시뮬레이션 실행", type="primary")

class BiologicalNetworkSimulator:
    def __init__(self, params):
        self.params = params
        self.network_data = {}
        
    def generate_nodes(self, network_type, node_count):
        """네트워크 타입에 따른 노드 생성"""
        nodes = []
        
        for i in range(node_count):
            if network_type == "관다발계 (식물)":
                node_type = self._get_plant_node_type(i, node_count)
                capacity = self._get_plant_capacity(node_type)
                position = self._get_plant_position(i, node_count, node_type)
            elif network_type == "신경계 (동물)":
                node_type = self._get_neural_node_type(i, node_count)
                capacity = self._get_neural_capacity(node_type)
                position = self._get_neural_position(i, node_count, node_type)
            elif network_type == "순환계 (동물)":
                node_type = self._get_circulatory_node_type(i, node_count)
                capacity = self._get_circulatory_capacity(node_type)
                position = self._get_circulatory_position(i, node_count, node_type)
            else:  # 균사체
                node_type = "hyphal"
                capacity = random.uniform(30, 80)
                position = (random.uniform(0, 100), random.uniform(0, 100))
            
            nodes.append({
                'id': i,
                'type': node_type,
                'x': position[0],
                'y': position[1],
                'capacity': capacity,
                'efficiency': random.uniform(0.7, 0.95),
                'is_damaged': random.random() < (self.params['network_damage'] / 100)
            })
            
        return nodes
    
    def _get_plant_node_type(self, i, total):
        if i == 0:
            return 'root'
        elif i < total * 0.3:
            return 'stem'
        else:
            return 'leaf'
    
    def _get_plant_capacity(self, node_type):
        capacities = {'root': 100, 'stem': 70, 'leaf': 40}
        return capacities.get(node_type, 50) + random.uniform(-10, 10)
    
    def _get_plant_position(self, i, total, node_type):
        if node_type == 'root':
            return (50, 10)
        elif node_type == 'stem':
            return (50 + random.uniform(-15, 15), 30 + (i / total) * 40)
        else:
            return (20 + random.uniform(0, 60), 70 + random.uniform(0, 25))
    
    def _get_neural_node_type(self, i, total):
        if i == 0:
            return 'brain'
        elif i < total * 0.2:
            return 'spinal'
        else:
            return 'peripheral'
    
    def _get_neural_capacity(self, node_type):
        capacities = {'brain': 120, 'spinal': 90, 'peripheral': 50}
        return capacities.get(node_type, 60) + random.uniform(-5, 5)
    
    def _get_neural_position(self, i, total, node_type):
        if node_type == 'brain':
            return (50, 90)
        elif node_type == 'spinal':
            return (50, 70 - (i / total) * 30)
        else:
            return (random.uniform(10, 90), random.uniform(10, 80))
    
    def _get_circulatory_node_type(self, i, total):
        if i == 0:
            return 'heart'
        elif i < total * 0.3:
            return 'artery'
        elif i < total * 0.7:
            return 'capillary'
        else:
            return 'vein'
    
    def _get_circulatory_capacity(self, node_type):
        capacities = {'heart': 150, 'artery': 80, 'capillary': 30, 'vein': 60}
        return capacities.get(node_type, 50) + random.uniform(-8, 8)
    
    def _get_circulatory_position(self, i, total, node_type):
        if node_type == 'heart':
            return (50, 50)
        else:
            angle = (i / total) * 2 * math.pi
            radius = {'artery': 25, 'capillary': 40, 'vein': 35}.get(node_type, 30)
            x = 50 + radius * math.cos(angle)
            y = 50 + radius * math.sin(angle)
            return (x, y)
    
    def generate_connections(self, nodes, network_type):
        """네트워크 타입에 따른 연결 생성"""
        connections = []
        
        if network_type == "관다발계 (식물)":
            connections = self._create_plant_connections(nodes)
        elif network_type == "신경계 (동물)":
            connections = self._create_neural_connections(nodes)
        elif network_type == "순환계 (동물)":
            connections = self._create_circulatory_connections(nodes)
        else:  # 균사체
            connections = self._create_mycelial_connections(nodes)
        
        return connections
    
    def _create_plant_connections(self, nodes):
        connections = []
        root_nodes = [n for n in nodes if n['type'] == 'root']
        stem_nodes = [n for n in nodes if n['type'] == 'stem']
        leaf_nodes = [n for n in nodes if n['type'] == 'leaf']
        
        # 뿌리 -> 줄기
        for root in root_nodes:
            for stem in stem_nodes:
                connections.append({
                    'from': root['id'],
                    'to': stem['id'],
                    'capacity': 80,
                    'efficiency': 0.9,
                    'transport_type': 'xylem'
                })
        
        # 줄기 -> 잎
        for stem in stem_nodes:
            nearby_leaves = sorted(leaf_nodes, 
                                 key=lambda l: abs(l['x'] - stem['x']) + abs(l['y'] - stem['y']))[:3]
            for leaf in nearby_leaves:
                connections.append({
                    'from': stem['id'],
                    'to': leaf['id'],
                    'capacity': 50,
                    'efficiency': 0.8,
                    'transport_type': 'xylem'
                })
        
        # 잎 -> 줄기 (체관)
        for leaf in leaf_nodes:
            nearest_stem = min(stem_nodes, 
                             key=lambda s: abs(s['x'] - leaf['x']) + abs(s['y'] - leaf['y']))
            connections.append({
                'from': leaf['id'],
                'to': nearest_stem['id'],
                'capacity': 40,
                'efficiency': 0.85,
                'transport_type': 'phloem'
            })
        
        return connections
    
    def _create_neural_connections(self, nodes):
        connections = []
        brain_nodes = [n for n in nodes if n['type'] == 'brain']
        spinal_nodes = [n for n in nodes if n['type'] == 'spinal']
        peripheral_nodes = [n for n in nodes if n['type'] == 'peripheral']
        
        # 뇌 -> 척수
        for brain in brain_nodes:
            for spinal in spinal_nodes:
                connections.append({
                    'from': brain['id'],
                    'to': spinal['id'],
                    'capacity': 100,
                    'efficiency': 0.95,
                    'transport_type': 'electrical'
                })
        
        # 척수 -> 말초신경
        for spinal in spinal_nodes:
            nearby_peripheral = sorted(peripheral_nodes,
                                     key=lambda p: abs(p['x'] - spinal['x']) + abs(p['y'] - spinal['y']))[:4]
            for peripheral in nearby_peripheral:
                connections.append({
                    'from': spinal['id'],
                    'to': peripheral['id'],
                    'capacity': 70,
                    'efficiency': 0.85,
                    'transport_type': 'electrical'
                })
        
        return connections
    
    def _create_circulatory_connections(self, nodes):
        connections = []
        heart_nodes = [n for n in nodes if n['type'] == 'heart']
        artery_nodes = [n for n in nodes if n['type'] == 'artery']
        capillary_nodes = [n for n in nodes if n['type'] == 'capillary']
        vein_nodes = [n for n in nodes if n['type'] == 'vein']
        
        # 심장 -> 동맥
        for heart in heart_nodes:
            for artery in artery_nodes:
                connections.append({
                    'from': heart['id'],
                    'to': artery['id'],
                    'capacity': 120,
                    'efficiency': 0.92,
                    'transport_type': 'arterial'
                })
        
        # 동맥 -> 모세혈관
        for artery in artery_nodes:
            nearby_capillaries = sorted(capillary_nodes,
                                      key=lambda c: abs(c['x'] - artery['x']) + abs(c['y'] - artery['y']))[:3]
            for capillary in nearby_capillaries:
                connections.append({
                    'from': artery['id'],
                    'to': capillary['id'],
                    'capacity': 60,
                    'efficiency': 0.8,
                    'transport_type': 'arterial'
                })
        
        # 모세혈관 -> 정맥
        for capillary in capillary_nodes:
            nearest_vein = min(vein_nodes,
                             key=lambda v: abs(v['x'] - capillary['x']) + abs(v['y'] - capillary['y']))
            connections.append({
                'from': capillary['id'],
                'to': nearest_vein['id'],
                'capacity': 40,
                'efficiency': 0.75,
                'transport_type': 'venous'
            })
        
        # 정맥 -> 심장
        for vein in vein_nodes:
            for heart in heart_nodes:
                connections.append({
                    'from': vein['id'],
                    'to': heart['id'],
                    'capacity': 80,
                    'efficiency': 0.85,
                    'transport_type': 'venous'
                })
        
        return connections
    
    def _create_mycelial_connections(self, nodes):
        connections = []
        
        # 균사체는 근거리 노드들을 서로 연결
        for i, node1 in enumerate(nodes):
            for j, node2 in enumerate(nodes):
                if i != j:
                    distance = math.sqrt((node1['x'] - node2['x'])**2 + (node1['y'] - node2['y'])**2)
                    if distance < 25:  # 근거리만 연결
                        connections.append({
                            'from': node1['id'],
                            'to': node2['id'],
                            'capacity': 45,
                            'efficiency': 0.7,
                            'transport_type': 'diffusion'
                        })
        
        return connections
    
    def simulate_nutrient_transport(self, network_type):
        """영양분 전달 시뮬레이션"""
        nodes = self.generate_nodes(network_type, self.params['network_nodes'])
        connections = self.generate_connections(nodes, network_type)
        
        # 환경 영향 계산
        temp_factor = 1 + (self.params['temperature'] - 25) * 0.02
        humidity_factor = self.params['humidity'] / 100
        ph_factor = 1 - abs(self.params['ph_level'] - 7) * 0.1
        
        total_transported = 0
        total_energy_used = 0
        total_time = 0
        pathway_efficiency = []
        
        for connection in connections:
            from_node = nodes[connection['from']]
            to_node = nodes[connection['to']]
            
            # 손상된 노드 체크
            if from_node['is_damaged'] or to_node['is_damaged']:
                effective_capacity = connection['capacity'] * 0.3
                effective_efficiency = connection['efficiency'] * 0.5
            else:
                effective_capacity = connection['capacity']
                effective_efficiency = connection['efficiency']
            
            # 환경 영향 적용
            environmental_factor = temp_factor * humidity_factor * ph_factor
            
            # 노이즈 추가
            noise = random.uniform(-self.params['noise_level'], self.params['noise_level'])
            
            # 실제 전달량 계산
            base_transport = min(
                effective_capacity,
                self.params['nutrient_concentration'] * effective_efficiency
            )
            
            actual_transport = base_transport * environmental_factor * (1 + noise)
            actual_transport = max(0, actual_transport)  # 음수 방지
            
            # 에너지 소비 계산
            energy_used = actual_transport * self.params['energy_consumption']
            
            # 전달 시간 계산
            distance = math.sqrt((from_node['x'] - to_node['x'])**2 + (from_node['y'] - to_node['y'])**2)
            transport_time = distance / self.params['transport_speed']
            
            total_transported += actual_transport
            total_energy_used += energy_used
            total_time += transport_time
            
            pathway_efficiency.append(actual_transport / energy_used if energy_used > 0 else 0)
        
        # 성능 지표 계산
        utilization_rate = (total_transported / (self.params['nutrient_concentration'] * len(connections))) * 100
        energy_efficiency = total_transported / total_energy_used if total_energy_used > 0 else 0
        avg_speed = total_transported / total_time if total_time > 0 else 0
        throughput = total_transported / (self.params['simulation_time'] / 60) if self.params['simulation_time'] > 0 else 0
        
        return {
            'network_type': network_type,
            'total_transported': total_transported,
            'utilization_rate': utilization_rate,
            'energy_efficiency': energy_efficiency,
            'avg_speed': avg_speed,
            'throughput': throughput,
            'total_energy_used': total_energy_used,
            'total_time': total_time,
            'pathway_efficiency': np.mean(pathway_efficiency) if pathway_efficiency else 0,
            'network_robustness': 1 - (sum(1 for n in nodes if n['is_damaged']) / len(nodes)),
            'nodes': nodes,
            'connections': connections
        }

def create_network_visualization(result):
    """네트워크 시각화"""
    nodes = result['nodes']
    connections = result['connections']
    
    # 노드 색상 매핑
    color_map = {
        'root': '#8B4513', 'stem': '#228B22', 'leaf': '#32CD32',
        'brain': '#FF69B4', 'spinal': '#4169E1', 'peripheral': '#87CEEB',
        'heart': '#DC143C', 'artery': '#FF6347', 'capillary': '#FFA07A', 'vein': '#4682B4',
        'hyphal': '#DDA0DD'
    }
    
    fig = go.Figure()
    
    # 연결선 추가
    for conn in connections:
        from_node = nodes[conn['from']]
        to_node = nodes[conn['to']]
        
        fig.add_trace(go.Scatter(
            x=[from_node['x'], to_node['x']],
            y=[from_node['y'], to_node['y']],
            mode='lines',
            line=dict(
                width=conn['capacity'] / 20,
                color=f"rgba(100, 150, 200, {conn['efficiency']})"
            ),
            showlegend=False,
            hoverinfo='skip'
        ))
    
    # 노드 추가
    for node_type in set(node['type'] for node in nodes):
        type_nodes = [n for n in nodes if n['type'] == node_type]
        
        fig.add_trace(go.Scatter(
            x=[n['x'] for n in type_nodes],
            y=[n['y'] for n in type_nodes],
            mode='markers+text',
            marker=dict(
                size=[n['capacity']/4 for n in type_nodes],
                color=[color_map.get(n['type'], '#666666') for n in type_nodes],
                line=dict(width=2, color='white'),
                opacity=[0.3 if n['is_damaged'] else 1.0 for n in type_nodes]
            ),
            text=[f"{n['type']}<br>{n['capacity']:.1f}" for n in type_nodes],
            textposition="middle center",
            name=node_type.title(),
            hovertemplate="<b>%{text}</b><br>효율성: %{customdata:.2f}<extra></extra>",
            customdata=[n['efficiency'] for n in type_nodes]
        ))
    
    fig.update_layout(
        title=f"{result['network_type']} 네트워크 구조",
        xaxis_title="X 좌표",
        yaxis_title="Y 좌표",
        showlegend=True,
        height=600,
        plot_bgcolor='rgba(0,0,0,0)',
        xaxis=dict(range=[0, 100]),
        yaxis=dict(range=[0, 100])
    )
    
    return fig

def create_performance_comparison(results):
    """성능 비교 차트"""
    if len(results) < 2:
        return None
    
    # 메트릭 데이터 준비
    metrics = ['utilization_rate', 'energy_efficiency', 'avg_speed', 'throughput', 'network_robustness']
    metric_names = ['이용률 (%)', '에너지 효율성', '평균 속도', '처리량', '네트워크 견고성']
    
    fig = make_subplots(
        rows=2, cols=3,
        subplot_titles=metric_names,
        specs=[[{"type": "bar"}, {"type": "bar"}, {"type": "bar"}],
               [{"type": "bar"}, {"type": "bar"}, {"type": "xy"}]]
    )
    
    network_names = list(results.keys())
    colors = ['#4CAF50', '#2196F3', '#FF9800', '#9C27B0', '#F44336']
    
    for i, (metric, name) in enumerate(zip(metrics, metric_names)):
        row = i // 3 + 1
        col = i % 3 + 1
        
        if i < 5:  # 바 차트
            values = [results[net][metric] for net in network_names]
            fig.add_trace(
                go.Bar(
                    x=network_names,
                    y=values,
                    name=name,
                    marker_color=colors[i % len(colors)],
                    showlegend=False
                ),
                row=row, col=col
            )
    
    # 레이더 차트 추가 (마지막 서브플롯)
    categories = ['이용률', '에너지효율', '속도', '처리량', '견고성']
    
    for i, network in enumerate(network_names):
        values = [
            results[network]['utilization_rate'] / 100,
            results[network]['energy_efficiency'] / max([results[n]['energy_efficiency'] for n in network_names]),
            results[network]['avg_speed'] / max([results[n]['avg_speed'] for n in network_names]),
            results[network]['throughput'] / max([results[n]['throughput'] for n in network_names]),
            results[network]['network_robustness']
        ]
        
        fig.add_trace(
            go.Scatterpolar(
                r=values + [values[0]],  # 다각형 닫기
                theta=categories + [categories[0]],
                fill='toself',
                name=network,
                line_color=colors[i % len(colors)]
            ),
            row=2, col=3
        )
    
    fig.update_layout(
        height=800,
        title_text="네트워크 성능 종합 비교",
        showlegend=True
    )
    
    return fig

# 시뮬레이션 실행
if run_simulation:
    # 매개변수 딕셔너리 생성
    params = {
        'nutrient_concentration': nutrient_concentration,
        'network_nodes': network_nodes,
        'transport_speed': transport_speed,
        'metabolic_efficiency': metabolic_efficiency,
        'diffusion_coeff': diffusion_coeff,
        'energy_consumption': energy_consumption,
        'temperature': temperature,
        'humidity': humidity,
        'ph_level': ph_level,
        'simulation_time': simulation_time,
        'noise_level': noise_level,
        'network_damage': network_damage
    }
    
    simulator = BiologicalNetworkSimulator(params)
    
    # 진행 표시
    progress_bar = st.progress(0)
    status_text = st.empty()
    
    # 시뮬레이션 실행
    results = {}
    
    if network_type == "모든 네트워크 비교":
        network_types = ["관다발계 (식물)", "신경계 (동물)", "순환계 (동물)", "균사체 (균류)"]
    else:
        network_types = [network_type]
    
    for i, net_type in enumerate(network_types):
        status_text.text(f"{net_type} 시뮬레이션 중...")
        progress_bar.progress((i + 1) / len(network_types))
        
        results[net_type] = simulator.simulate_nutrient_transport(net_type)
        time.sleep(0.5)  # 시각적 효과
    
    progress_bar.empty()
    status_text.empty()
    
    # 결과 표시
    st.success("✅ 시뮬레이션 완료!")
    
    # 최적 네트워크 찾기
    if len(results) > 1:
        best_network = max(results.keys(), 
                          key=lambda k: results[k]['energy_efficiency'] * results[k]['utilization_rate'])
        
        # 메트릭 표시
        col1, col2, col3, col4 = st.columns(4)
        
        with col1:
            st.metric("🏆 최적 네트워크", best_network.split(' ')[0])
        
        with col2:
            st.metric("💚 영양분 이용률", f"{results[best_network]['utilization_rate']:.1f}%")
        
        with col3:
            st.metric("⚡ 에너지 효율성", f"{results[best_network]['energy_efficiency']:.2f}")
        
        with col4:
            st.metric("🔋 네트워크 견고성", f"{results[best_network]['network_robustness']:.1f}")
    
    # 결과 테이블
    st.subheader("📋 성능 비교 결과")
    
    df_results = pd.DataFrame({
        net: {
            '총 전달량 (mg)': f"{result['total_transported']:.2f}",
            '이용률 (%)': f"{result['utilization_rate']:.1f}",
            '에너지 효율성': f"{result['energy_efficiency']:.2f}",
            '평균 속도 (cm/s)': f"{result['avg_speed']:.2f}",
            '처리량 (mg/min)': f"{result['throughput']:.2f}",
            '경로 효율성': f"{result['pathway_efficiency']:.2f}",
            '네트워크 견고성': f"{result['network_robustness']:.2f}"
        }
        for net, result in results.items()
    }).T
    
    st.dataframe(df_results, use_container_width=True)
    
    # 성능 비교 차트
    if len(results) > 1:
        st.subheader("📊 성능 비교 차트")
        fig_comparison = create_performance_comparison(results)
        if fig_comparison:
            st.plotly_chart(fig_comparison, use_container_width=True)
    
    # 네트워크 시각화
    st.subheader("🌐 네트워크 구조 시각화")
    
    if len(results) == 1:
        # 단일 네트워크
        net_name = list(results.keys())[0]
        fig_network = create_network_visualization(results[net_name])
        st.plotly_chart(fig_network, use_container_width=True)
    else:
        # 다중 네트워크
        cols = st.columns(2)
        for i, (net_name, result) in enumerate(results.items()):
            with cols[i % 2]:
                fig_network = create_network_visualization(result)
                st.plotly_chart(fig_network, use_container_width=True)
    
    # 분석 및 인사이트
    st.subheader("🔬 생물학적 분석 및 인사이트")
    
    with st.expander("📈 성능 분석"):
        if len(results) > 1:
            best_utilization = max(results.values(), key=lambda x: x['utilization_rate'])
            best_efficiency = max(results.values(), key=lambda x: x['energy_efficiency'])
            best_speed = max(results.values(), key=lambda x: x['avg_speed'])
            best_robustness = max(results.values(), key=lambda x: x['network_robustness'])
            
            st.write(f"""
            **성능 분석 결과:**
            
            🎯 **최고 영양분 이용률**: {best_utilization['network_type']} ({best_utilization['utilization_rate']:.1f}%)
            - 이는 공급된 영양분을 가장 효율적으로 활용함을 의미합니다.
            
            ⚡ **최고 에너지 효율성**: {best_efficiency['network_type']} ({best_efficiency['energy_efficiency']:.2f})
            - 단위 에너지당 가장 많은 영양분을 전달할 수 있습니다.
            
            🚀 **최고 전달 속도**: {best_speed['network_type']} ({best_speed['avg_speed']:.2f} cm/s)
            - 긴급한 영양분 공급이 필요한 상황에서 유리합니다.
            
            🛡️ **최고 견고성**: {best_robustness['network_type']} ({best_robustness['network_robustness']:.2f})
            - 네트워크 손상에 대한 저항성이 가장 높습니다.
            """)
        else:
            result = list(results.values())[0]
            st.write(f"""
            **{result['network_type']} 분석 결과:**
            
            - 영양분 이용률: {result['utilization_rate']:.1f}%
            - 에너지 효율성: {result['energy_efficiency']:.2f}
            - 평균 전달 속도: {result['avg_speed']:.2f} cm/s
            - 네트워크 견고성: {result['network_robustness']:.2f}
            """)
    
    with st.expander("🌿 관다발계 (식물) 특성"):
        st.write("""
        **구조적 특징:**
        - **물관 (Xylem)**: 뿌리에서 잎으로 물과 무기염류 운반
        - **체관 (Phloem)**: 잎에서 전체로 광합성 산물 운반
        - **일방향성**: 물관은 상향, 체관은 하향+상향 이동
        
        **생물학적 메커니즘:**
        - **증산작용**: 잎의 기공을 통한 수분 증발로 흡인력 생성
        - **근압**: 뿌리의 삼투압으로 물을 밀어올림
        - **동반세포**: 체관의 당 농도 조절
        
        **효율성 요인:**
        - 중력과 증산작용을 이용한 에너지 절약
        - 이중 시스템으로 서로 다른 물질 전용 경로
        - 계절별 활동 조절 가능
        """)
    
    with st.expander("🧠 신경계 (동물) 특성"):
        st.write("""
        **구조적 특징:**
        - **중추신경계**: 뇌와 척수, 정보 처리 및 명령 전달
        - **말초신경계**: 감각신경과 운동신경, 정보 수집 및 실행
        - **시냅스**: 신경세포 간 연결점, 신호 전달 및 조절
        
        **생물학적 메커니즘:**
        - **활동전위**: 나트륨-칼륨 펌프를 통한 전기신호 생성
        - **신경전달물질**: 시냅스에서 화학적 신호 전달
        - **수초**: 축삭을 감싸 신호 전달 속도 증가
        
        **효율성 요인:**
        - 전기신호를 통한 초고속 전달 (최대 120m/s)
        - 선택적 신호 증폭과 억제
        - 학습과 기억을 통한 최적화
        """)
    
    with st.expander("❤️ 순환계 (동물) 특성"):
        st.write("""
        **구조적 특징:**
        - **심장**: 4개 방으로 구성된 강력한 펌프
        - **동맥**: 심장에서 조직으로 산소가 풍부한 혈액 운반
        - **정맥**: 조직에서 심장으로 이산화탄소 풍부한 혈액 운반
        - **모세혈관**: 조직과 혈액 간 물질 교환
        
        **생물학적 메커니즘:**
        - **심박출량**: 분당 펌핑되는 혈액량 조절
        - **혈관수축/이완**: 혈압과 혈류량 조절
        - **적혈구**: 헤모글로빈을 통한 산소 운반
        
        **효율성 요인:**
        - 압력 차이를 이용한 강제 순환
        - 이중 순환계 (폐순환 + 체순환)
        - 실시간 혈류량 조절
        """)
    
    with st.expander("🍄 균사체 (균류) 특성"):
        st.write("""
        **구조적 특징:**
        - **균사**: 가는 실 모양의 기본 구조
        - **균사체**: 균사들이 얽혀 형성된 네트워크
        - **격벽**: 균사 내 구획을 나누는 벽
        
        **생물학적 메커니즘:**
        - **원형질 흐름**: 세포질의 방향성 있는 이동
        - **삼투압 조절**: 물과 영양분 흡수 조절
        - **효소 분비**: 외부 유기물 분해 및 흡수
        
        **효율성 요인:**
        - 분산된 망상 구조로 높은 복원력
        - 다중 경로를 통한 최적 경로 탐색
        - 환경 변화에 대한 빠른 적응
        - 공생 관계를 통한 상호 이익
        """)
    
    with st.expander("🔬 환경 영향 분석"):
        st.write(f"""
        **현재 환경 조건이 네트워크에 미치는 영향:**
        
        🌡️ **온도 ({temperature}°C)**:
        - 최적 온도(25°C)에서 {abs(temperature-25)*2:.1f}% {'증가' if temperature > 25 else '감소'}
        - 온도가 높을수록 분자 운동이 활발해져 전달 속도 증가
        - 너무 높으면 단백질 변성, 너무 낮으면 대사 저하
        
        💧 **습도 ({humidity}%)**:
        - 현재 습도는 전달 효율성에 {humidity}% 영향
        - 식물의 경우 습도가 낮으면 증산작용 증가
        - 동물의 경우 습도가 혈액 점성에 영향
        
        🧪 **pH ({ph_level})**:
        - 중성(pH 7.0)에서 {abs(ph_level-7)*10:.1f}% {'저하' if ph_level != 7 else '최적'}
        - pH 변화는 효소 활성과 막 투과성에 영향
        - 극단적 pH는 단백질 구조 변성 유발
        
        📊 **네트워크 손상률 ({network_damage}%)**:
        - {network_damage}%의 노드가 손상된 상태
        - 손상된 네트워크는 우회 경로 활용 필요
        - 균사체와 순환계가 손상에 가장 강함
        """)
    
    # 실험 제안
    st.subheader("🧪 추가 실험 아이디어")
    
    col1, col2 = st.columns(2)
    
    with col1:
        st.info("""
        **🔬 심화 실험**
        
        1. **스트레스 반응 시뮬레이션**
           - 갑작스러운 환경 변화
           - 병원균 침입 상황
           - 영양분 부족 상태
        
        2. **최적화 실험**
           - 네트워크 구조 개선
           - 에너지 소비 최소화
           - 전달 속도 최대화
        
        3. **진화 시뮬레이션**
           - 세대별 네트워크 진화
           - 환경 적응 과정
           - 선택압에 따른 변화
        """)
    
    with col2:
        st.success("""
        **🌱 실제 응용 분야**
        
        1. **농업 기술**
           - 스마트팜 관개 시스템
           - 식물 영양 상태 모니터링
           - 작물 품종 개량
        
        2. **의학 연구**
           - 약물 전달 시스템
           - 혈관 질환 치료
           - 신경 재생 치료
        
        3. **바이오미메틱스**
           - 효율적 배송 네트워크
           - 자가 치유 재료
           - 적응형 통신 시스템
        """)

# 실시간 모니터링 (옵션)
if st.sidebar.checkbox("실시간 모니터링 모드"):
    st.subheader("📡 실시간 영양분 전달 모니터링")
    
    # 실시간 데이터 플레이스홀더
    chart_placeholder = st.empty()
    metric_placeholder = st.empty()
    
    if st.sidebar.button("모니터링 시작"):
        for i in range(20):  # 20초간 모니터링
            # 실시간 데이터 생성
            time_data = list(range(max(0, i-10), i+1))
            
            # 각 네트워크의 실시간 성능
            real_time_data = {}
            for net_type in ["관다발계 (식물)", "신경계 (동물)", "순환계 (동물)", "균사체 (균류)"]:
                real_time_data[net_type] = [
                    random.uniform(50, 100) + random.uniform(-10, 10) for _ in time_data
                ]
            
            # 실시간 차트 업데이트
            fig_realtime = go.Figure()
            colors = ['#4CAF50', '#2196F3', '#FF9800', '#9C27B0']
            
            for j, (net_type, data) in enumerate(real_time_data.items()):
                fig_realtime.add_trace(go.Scatter(
                    x=time_data,
                    y=data,
                    mode='lines+markers',
                    name=net_type.split(' ')[0],
                    line=dict(color=colors[j], width=3)
                ))
            
            fig_realtime.update_layout(
                title="실시간 영양분 전달 효율성",
                xaxis_title="시간 (초)",
                yaxis_title="전달 효율성 (%)",
                height=400
            )
            
            chart_placeholder.plotly_chart(fig_realtime, use_container_width=True)
            
            # 실시간 메트릭
            current_values = {k: v[-1] for k, v in real_time_data.items()}
            best_current = max(current_values, key=current_values.get)
            
            with metric_placeholder.container():
                cols = st.columns(4)
                for idx, (net, value) in enumerate(current_values.items()):
                    with cols[idx]:
                        delta = random.uniform(-5, 5)
                        st.metric(
                            net.split(' ')[0],
                            f"{value:.1f}%",
                            f"{delta:+.1f}%"
                        )
            
            time.sleep(1)

# 데이터 내보내기
if 'results' in locals() and results:
    st.subheader("💾 데이터 내보내기")
    
    # CSV 다운로드
    if st.button("📊 결과 데이터 CSV 다운로드"):
        df_export = pd.DataFrame(results).T
        csv = df_export.to_csv()
        st.download_button(
            label="CSV 파일 다운로드",
            data=csv,
            file_name=f"nutrient_network_simulation_{datetime.now().strftime('%Y%m%d_%H%M%S')}.csv",
            mime="text/csv"
        )
    
    # 상세 보고서 생성
    if st.button("📄 상세 분석 보고서 생성"):
        with st.expander("📋 상세 분석 보고서", expanded=True):
            st.markdown(f"""
            # 영양분 전달 네트워크 분석 보고서
            
            **분석 일시**: {datetime.now().strftime('%Y년 %m월 %d일 %H시 %M분')}
            
            ## 실험 조건
            - 영양분 농도: {nutrient_concentration} mg/L
            - 네트워크 노드 수: {network_nodes}개
            - 전달 속도: {transport_speed} cm/s
            - 대사 효율성: {metabolic_efficiency}%
            - 온도: {temperature}°C
            - 습도: {humidity}%
            - pH: {ph_level}
            - 네트워크 손상률: {network_damage}%
            
            ## 주요 결과
            """)
            
            for net_type, result in results.items():
                st.markdown(f"""
                ### {net_type}
                - **총 전달량**: {result['total_transported']:.2f} mg
                - **이용률**: {result['utilization_rate']:.1f}%
                - **에너지 효율성**: {result['energy_efficiency']:.2f}
                - **평균 속도**: {result['avg_speed']:.2f} cm/s
                - **처리량**: {result['throughput']:.2f} mg/min
                - **네트워크 견고성**: {result['network_robustness']:.2f}
                """)
            
            if len(results) > 1:
                best_overall = max(results.keys(), 
                                 key=lambda k: results[k]['energy_efficiency'] * results[k]['utilization_rate'])
                st.markdown(f"""
                ## 결론
                
                현재 실험 조건에서 **{best_overall}**이 가장 우수한 성능을 보였습니다.
                
                이는 해당 네트워크의 구조적 특성과 환경 조건이 잘 맞아떨어진 결과로 해석됩니다.
                """)

# 푸터
st.markdown("---")
st.markdown("""
<div style="text-align: center; color: #666; font-size: 0.9em;">
🌱 <strong>영양분 전달 네트워크 효율성 시뮬레이션</strong><br>
생물학적 시스템의 아름다운 효율성을 탐구합니다 | Made with Streamlit
</div>
""", unsafe_allow_html=True)
